---
layout: post
permalink: /how-to-dynamic-customize-django-forms
title: Django:我是怎么做到使用django动态定义表单(form)的
---

# Django:我是怎么做到使用django动态定义表单(form)的 #

## 第一节 数据结构 ##

最近的项目开发,用到了这样的数据结构:

主要是来管理设备的.

下面我列一下它简化的结构,不需要更多了,其余的只是多余或者说是锦上添花:

设备分类(相当于设备的大分类)的数据结构:

    class Category(models.Model):
        name = models.CharField(max_length=40)

具体设备的数据结构:

    class Equipment(models.Model):
        name        = models.CharField(max_length=100)
        category    = models.ForeignKey(Category, related_name="cg_equip_list")

设备参数的数据结构:

    class Characteristic(models.Model):
        category  = models.ForeignKey(Category, related_name="eq_characteristics")
        name      = models.CharField(max_length=40)

设备参数的值的数据结构:

    class CharacteristicValue(models.Model):
        equipment       = models.ForeignKey(Equipment, related_name="eq_characteristic_values")
        characteristic  = models.ForeignKey(Characteristic, related_name="eq_key_values")
        value           = models.CharField(max_length=100)

可能好多人会问:你怎么不把设备的参数值直接定义到设备的数据结构中去呢?

我的回答就是:这样可以自己定义更多的设备.

每个设备的参数是不一样的,比如说电脑的参数基本上就是:CPU频率,内存大小,硬盘大小,显示器类型,显示器大小等等.

而打印机的参数可以这样:最大打印纸张幅面,打印机类型等等.

这样两种有着不同参数的设备该怎么将它们的数据保存至数据库中呢?

按照刚才上面的提问,我们将需要建立2个表,电脑表和打印机表,分别包含相应的参数在列中.这样的话,如果再多一种设备,比如照相机,我们又得多建立一张数据表,而疲于维护数据库了.

但是按照参数与设备分离,以参数表的形式定义各类设备的参数,以外键链接到设备分类表.

再将具体设备的各项参数的值放入参数值表中,就可以保存各个设备的参数了.

参数值的数据表中,一个参数属于哪个设备,主要根据设备的id,和参数的id来确定.

这样的表结构就相对来说比较通用.

比较麻烦的就是,增加一个设备的时候,同时要增加它相应的参数值,它的参数不在设备表中,这样就要通过参数表来查询:

在参数表中,参数的设备分类id=具体设备的所属设备分类id的行就是该设备所拥有的参数.

比如说:设备分类表中数据:

    id  name
    1   台式计算机
    2   打印机

参数在参数表中保存了以下几行数据:

    id name      category
    1  CPU频率    1
    2  MAC地址    1
    3  内存大小   1
    4  显示器大小 1
    5  显示器类型 1
    6  硬盘大小   1
    7  最大打印幅面 2

可以一目了然的看出台式计算机有6个参数,打印机有1个参数.

##第二节 解决方案的选用

数据结构搞清楚了,现在的问题是怎么录入这些参数的值呢?

困难在于,每种设备的参数的个数是不确定的,因此,在定义form的时候,我们不能固定的定死form的 field个数,只有动态的增减field的个数.

那么,怎么动态生成一个有不同数目field的form呢?

这里,我的想法就是,既然参数个数保存在数据库中了,那么肯定是要查询数据库来动态生成了.

那么,使用django,该用什么具体的方法呢?

我自己是试过不少的方法的,也是在调试的过程中慢慢找到了解决这个问题的方法.

过程是这样的:

我开始想的解决的方案有:

1.使用ajax来动态的生成form
2.使用admin中的方法,用inline的 方法,在增加一个设备的同时增加几个参数
3.使用formset来制作
4.使用formtools中的formwizard(表单向导)来制作.

问题的困境主要在于参数值的表中有2个外键,所以不好确定该怎么弄.

前面三种方法我仅仅试了一下,调试了一两天就放弃了,主要原因就是:

1.ajax我目前还是不太熟,要想用好它,又得转向去学习一些相关的javascript库,一时半会弄不好.
2.admin中的inline是针对一种数据model的,增加的参数值也是相同的,比如说可能增加成6个CPU参数.而且是用在admin中的,想抠出来自己使用还挺麻烦的.
3.formset中的form也是要求相同的数据模型,但是参数值表中2个外键高的你根本没法使用formset,formset是在简单的数据结构情况下录入数据用的.

formset有一个函数就是add_fields,这个函数比较有用,但是form没有这个函数.了解了原理就简单了,就是form['field_name']=forms.CharField()之类就可以为一个form 实例添加field了.

我觉得formset是可以解决问题的,但是前提是构造formset前,必须提前知道formset中有几个参数值的form在里面(就是要知道某种分类的设备有几个参数,好在formset中构建几个form,CharacteristicValue的form,每个form采用不同的参数来进行初始化initial).

在一个页面中,先增加设备的form,因为设备的参数个数不是固定的,需要动态的增加含有几个参数值表单的formset,这样就可以选择设备的参数了.formset比较适用于使用固定个数的form.所以这样就给使用formset增加了难度,如果要让这种方式能够使用,最终还是要用到ajax来根据用户选择设备类型的时候,动态的生成formset.

formset需要在view中初始化.

所以这种方式我没有采用.

我采用的是表单向导的方式.

django的contrib中提供了formtools,用过了,会用了才觉得真好用.我是看英文文档一点一滴的学会使用的,感觉真的很方便.

但是上面的4的解决方案的实现过程倒是很艰难的.我弄了3天才弄出点眉目来.

##第三节 具体的实现

django中表单向导使用起来很简单的.

    from django.utils.translation import ugettext_lazy as _
    from django import forms
    from django.forms.formsets import BaseFormSet
    from django.forms.fields import FileField
    from django.forms.util import ValidationError
    from django.shortcuts import render_to_response
    from django.contrib.formtools.wizard import FormWizard
    from ddtcms.office.equipment.models import Equipment,Characteristic,CharacteristicValue
    class EquipmentForm(forms.ModelForm):
        class Meta:
            model = Equipment
    class CharacteristicValueForm(forms.Form):
        def clean(self):
            a=self.fields
            s=self.data
            self.cleaned_data = {}
            # 下面的这一段for 是从 django的forms.py中的 full_clean 中复制来的
            for name, field in self.fields.items():
                # value_from_datadict() gets the data from the data dictionaries.
                # Each widget type knows how to retrieve its own data, because some
                # widgets split data over several HTML fields.
                value = field.widget.value_from_datadict(self.data, self.files, self.add_prefix(name))
                try:
                    if isinstance(field, FileField):
                        initial = self.initial.get(name, field.initial)
                        value = field.clean(value, initial)
                    else:
                        value = field.clean(value)
                    self.cleaned_data[name] = value
                    if hasattr(self, 'clean_%s' % name):
                        value = getattr(self, 'clean_%s' % name)()
                        self.cleaned_data[name] = value
                except ValidationError, e:
                    self._errors[name] = self.error_class(e.messages)
                    if name in self.cleaned_data:
                        del self.cleaned_data[name]
            #cl=self.cleaned_data
            #debug()

EquipmentCreateWizard其实也可以放在views.py中,而且我觉得更合理一点.

在EquipmentCreateWizard 中,我试着修改过process_step 函数,但是得不到正确的结果,后来修改了get_form,都是想从django的formtools的wizard.py中复制过来再进行修改的.

get_form的修改也没有得到正确的结果.后来就修改render函数,在第2步的时候,我将动态参数个数显示出来了.但是到最后结束done的环节,取得的formdata中,第二个form没有数据,就是一个空的{},

于是我又重新修改get_form函数,无非就是判断是不是第二步,然后给第二个form动态添加几个field:

    if step == 1:
        cg       = old_data.get('0-category', 1)
        cs       = Characteristic.objects.all().filter(category__id=cg)
        for c in cs:
            form.fields['Characteristic-'+str(c.id)] = forms.CharField(label = c.name)
        g=form.fields
        #debug()

这段代码在get_form和 render中都有,都是判断是不是第2步,然后就根据第1步中选择的设备的分类来查询到具体的分类,再根据分类来获取该种分类的设备有哪些参数,然后根据参数个数修改form的参数field的个数.

'Characteristic-'+str(c.id)是用来以后保存数据的时候,split这个字符串,得到参数的id,并在参数值表中保存Characteristic-1,Characteristic-2...的value.

    g=form.fields
    #debug()

用来断点查看参数field有多少个,是否修改成功.

=========================

    from django.conf.urls.defaults import *
    from ddtcms.office.equipment.forms import EquipmentForm,CharacteristicValueForm,EquipmentCreateWizard
    urlpatterns = patterns('ddtcms.office.equipment.views',
        url(r'^$', 'index', name="equipment_index"),
        url(r'^add/$', 'equipment_create', name="equipment_create"),
        url(r'^add-by-wizard/$',EquipmentCreateWizard([EquipmentForm, CharacteristicValueForm]), name="equipment_create_by_wizard"), )
        以上代码,csdnbolg 自动过滤了 $符号,我加了上去,可能有不对的地方.

==========================

wizard_0.html

    {% block content %}
    <h2>添加/修改设备向导</h2>
    第 {{ step }} 步, 共 {{ step_count }} 步.
    填写设备基本情况
    {% csrf_token %}
    {{ form }}
    {{ previous_fields|safe }}
    {% endblock %}

===================

wizard_1.html

    {% block content %}
    <h2>添加/修改设备向导</h2>
    第 {{ step }} 步, 共 {{ step_count }} 步.
    填写设备参数, 如果没有要填写的内容, 请直接点击确定.
        {% csrf_token %}
                {{ form }}
            {{ previous_fields|safe }}
    {% endblock %}

====================

done.html

    {% block content %}

    <h2>添加/修改设备向导</h2>
    您已经成功添加了一个设备.
        {{form_data}}
    {% endblock %}

============

还可以用另外的form来实现formwizard,就是第一个form1,主要用来让用户选择设备的分类,form2就根据前面的来动态生成参数的表单.原理是一样的.

还有就是写2个view来模拟formwizard,第一个view增加一个设备,第二个view带设备id这个参数即可,可以很有效的增加设备的参数.