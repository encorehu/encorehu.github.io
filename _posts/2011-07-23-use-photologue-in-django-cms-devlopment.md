---
layout: post
permalink: /use-photologue-in-django-cms-devlopment
title: Django:在DDTCMS中使用Photologue做相册并增加封面的探索
---

# Django:在DDTCMS中使用Photologue做相册并增加封面的探索 #

			写于2010年11月6日

今天主要是想把admin中的一个图集的cover 在编辑的时候，限定为该图集所属的图片中，但是没有发现好的方法。我使用了2种尝试。

一种是在models.py中的gallery的init函数中初始化cover的可选值：



    #def __init__(self, *args, **kwargs):

    #    #self.cover.choices = self.photos.all()

    #    o=self.cover

    #    super(Gallery, self).__init__(*args, **kwargs)

    #    #self.set_cover_choices()

    #    self.cover.queryset = self.photos.all()





    #def set_cover_choices(self):

    #    p=self.photos.all()

    #    o=type(self.cover)

    #    self.cover.choices = self.photos.all()




但是init根本不行，说cover是个nonetype，也就是说cover根本 就没有被初始化。

第二种就是改admin.py了



    class GalleryAdmin(admin.ModelAdmin):



        list_display = ('title', 'date_added', 'photo_count', 'is_public')



        list_filter = ['date_added', 'is_public']



        date_hierarchy = 'date_added'



        prepopulated_fields = {'title_slug': ('title',)}



        filter_horizontal = ('photos',)







        def formfield_for_dbfield(self, db_field, **kwargs):



            field = super(GalleryAdmin, self).formfield_for_dbfield(db_field, **kwargs) # Get the default field



            if db_field.name == 'cover':



                my_choices = [('', '---------'),('1',"fff")]



                #my_choices.extend(Photo.objects.filter(gallery=实例化的gallery).values_list('id','name'))



                #print my_choices



                d=dir(self)



                p=dir(self.model)



                q=self.model.cover



                g()



                field.choices = my_choices



            return field




但是问题是，不知道怎么获取该GalleryAdmin具体实例化时使用的Gallery是哪个，因此搁浅。

另外通过查看admin的代码，知道



        # DEPRECATED methods.



        #



    def __call__(self, request, url):



    This function still exists for backwards-compatibility; it will be



            removed in Django 1.3.




也就是说在django1.3中，这种call的方式来获取request的方法，进而获取当前登录用户的方法可能要失败了。这个我在blog中使用过，从国外的朋友那里借过来的方法。

 想了又想，admin真的是个鸡肋，看来以后的方向就是减少甚至不在admin的界面上作什么优化了，把功夫下在前台上。Admin的管理功能的增强还不如放在前面呢，这样在view中处理什么都很方便的。

傻逼的admin。

实在不行的话，我只好再下点精力转向pylons那种自由的东西了，告别django种种的鸡肋模块，如orm，如urlconf，如模板渲染语言，等等，真的是，要找django的缺点，真是一大堆。