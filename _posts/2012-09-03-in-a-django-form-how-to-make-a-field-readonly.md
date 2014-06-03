---
layout: post
permalink: /in-a-django-form-how-to-make-a-field-readonly
title: django项目中如何使表单中的一个域只读
---

# django项目中如何使表单中的一个域只读 #


文章编译自  http://stackoverflow.com/questions/324477/in-a-django-form-how-to-make-a-field-readonly-or-disabled-so-that-it-cannot-b

有时候会遇到要求某个字段在编辑的时候保持只读状态，这样用户就不能编辑它 了。

stackoverflow上的这篇文章是这样解决的。

model：

    class Item(models.Model):

        sku = models.CharField(max_length=50)

        description = models.CharField(max_length=200)

        added_by = models.ForeignKey(User)


form：

    class ItemForm(ModelForm):

        def __init__(self, *args, **kwargs):

            super(ItemForm, self).__init__(*args, **kwargs)

            instance = getattr(self, 'instance', None)

            if instance and instance.id:

                self.fields['sku'].widget.attrs['readonly'] = True



        def clean_sku(self):

            return self.instance.sku
    问题补充还说可以设置该字段的表单域为disabled：


    self.fields['sku'].widget.attrs['disabled'] = True