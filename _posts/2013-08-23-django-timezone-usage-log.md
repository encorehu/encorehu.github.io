---
layout: post
permalink: /django-timezone-usage-log
title: Django的timezone应用心得
---

# Django的timezone应用心得 #


自从Django1.4开始, django之中就开始使用timezone这个玩意了, 换成中文, 就是时区的意思.

中国地区幅员辽阔, 按照15度一个时区计算,中国横跨5个时区, 但是实际上我们使用的是北京时间的东八区时间, 就是在国际标准时间的基础上, 早了8个小时. 也就是说北京这边看到日出了, 8小时后位于英国的国际标准时间起点的位置才能看到日出.

django既然开始用这个时区了, 那我们也要跟上啊. 结果就是出现了很多意想不到的错误.

#1. 大量的 RuntimeWarning: DateTimeField received a naive datetime (YYYY-MM-DD HH:MM:SS) while time zone support is active

这种情况就是你的settings.py中的 USE_TZ=True 了, 一旦你设置了这个地方, 你的噩梦就开始了.哈哈, 如果为了眼不见心不烦, 你可以将它设置为False:  USE_TZ = False, 这样, django1.4中出现的timezone你完全不必理会, 还是按照你以前的设置来运行程序.

#2. now.date()居然是昨天, 或者明天.

实际上, 确实会遇到这种问题.

举个实例来说吧:

有新闻(news)模块, News model有个字段为 pub_date, 然后, 你设置客户端访问该条新闻的url为 http://example.com/news/2013/08/23/10086/ 就是按照  /新闻/年/月/日/ID/ 的方式编排, 结果你发现: http://example.com/news/2013/08/23/10086/ 居然是404, 找不到这个新闻, 到数据库中一看, 实际上是存在这条新闻的, 但是为什么就是看不到这个新闻呢?

经过看文档和django源码, 我终于找到了原因:

先看News model的简单定义:

    	class News(models.Model):

    	    title          = models.CharField(max_length=200)

    	    pub_date       = models.DateTimeField('date published',blank=True,default=timezone.now)

    	    content        = models.TextField()



    	    def get_absolute_url(self):

    	        return "/news/%s/%d/" % (self.pub_date.strftime("%Y/%m/%d").lower(), self.pk)

1.可以看到, pub_date我已经让它默认使用django的timezone.now函数来获取默认时间了(这个时间是支持timezone的, 这个timezone的值, 就是 settings.py中定义的 TIME_ZONE = 'Asia/Shanghai' (中国只有2个时区字符串, 一个是上海'Asia/Shanghai', 一个是重庆'Asia/Chongqing', 以前的政府所在地, 千万不要用北京, 这个在国际标准中是不存在的)

2. 那么self.pub_date应该是django中的aware_time了, 那么这个self.pub_date 的date 为啥不对, 导致找不到这条新闻呢?原因后来我终于发现了, django 在存储 self.pub_date到数据库的时候, 好像是保存为 UTC 时区的, 也就是说这个self.pub_date和你的服务器项目的settings.py中设置好的时区 'Asia/Shanghai' 是不一样的, 也就是说, 最后得到的date可能不是同一天. 所以

self.pub_date.strftime("%Y/%m/%d") 获得的日期的表示, 可能不是你的时区的日期, 而是UTC时区的日期

这里, 你的时区的日期只的是你的服务器的项目设置里面的时区,  'Asia/Shanghai' ,我设置为这个, 肯定是以服务器时间为准的, 因此, 用户访问 新闻/日期/id 的时候, 这个日期不是服务器时区的日期的时候, 会发现出现404错误. 不过因为只有8小时的时差, 这种错误有时候不会出现. 因为差了8小时也可能是同一天. 如果是凌晨1点发的, 差8小时可能就会在日期上相隔一天.因此就出现了错误.

那么你肯定要说, 那为什么不以数据库中保存的utc时区来生成url呢, 这样就不会有时区的差别了. 实际上我上面的代码就是这么做的, 那么为什么还会在date日期上有差别呢?通过找源码, 我发现了在 django\views\generic\dates.py 文件的292行开始的2个函数里

        class DateMixin(object): 里

        def _make_date_lookup_arg(self, value):

            """

            Convert a date into a datetime when the date field is a DateTimeField.

            When time zone support is enabled, date is assumed to be in the

            current time zone, so that displayed items are consistent with the URL.

            """

            if self.uses_datetime_field:

                value = datetime.datetime.combine(value, datetime.time.min)

                if settings.USE_TZ:

                    value = timezone.make_aware(value, timezone.get_current_timezone())

            return value



        def _make_single_date_lookup(self, date):

            """

            Get the lookup kwargs for filtering on a single date.

            If the date field is a DateTimeField, we can't just filter on

            date_field=date because that doesn't take the time into account.

            """

            print date

            date_field = self.get_date_field()

            print date_field

            if self.uses_datetime_field:

                since = self._make_date_lookup_arg(date)

                until = self._make_date_lookup_arg(date + datetime.timedelta(days=1))





                print '    since',since

                print '    until',until

                return {

                    '%sgte' % date_field: since,

                    '%slt' % date_field: until,

                }

            else:

                # Skip self._make_date_lookup_arg, it's a no-op in this branch.

                return {date_field: date}



注意since 和 until, 也就是BaseDateDetailView中的get_object 最后会调用这2个函数, 实际上

BaseDateDetailView是对 新闻/年/月/日/id 进行分解调用, 解析出了 请求的url中的年与日, 再组合成 2013-08-23  这样的字符串, 然后在数据库中进行过滤, 取得 2013-08-23 到 2013-08-24 日期之间的所有新闻集, 再在这个新闻集中找 具有id的新闻.

问题就是

        since 2013-08-23 00:00:00+08:00

        until 2013-08-24 00:00:00+08:00

这种时间是时区为 asia/shanghai 的, 也就是你的settings.py中设置的时区, 和UTC时区相差了8个小时, 因此将数据库中的UTC时区的日期按照

settings.py中设置的时区Asia/Shanghai 来过滤, 结果居然没有这一天的新闻.

那么解决的办法就是修改 url 中的年月日生成方式, 按照 settings.py中设置好的时区, 来生成年月日的url

因此, 按照settings.py中设置好的时区, 来更正url中的年月日, 就不会再出现问题了.



        def get_absolute_url(self):

        # 获取settings中的时区

        current_tz = timezone.get_current_timezone()

        # 按照该时区格式化数据库中存储的UTC时区时间self.pub_date为 settings中的时区

        db_local_time = current_tz.normalize(self.pub_date)

        # 取出date()并生成url

        return "/news/%s/%d/" % (db_local_time.date().strftime("%Y/%m/%d").lower(), self.pk)