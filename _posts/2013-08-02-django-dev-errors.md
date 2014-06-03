---
layout: post
permalink: /django-dev-errors
title: Django开发过程中遇到的错误
---

# Django开发过程中遇到的错误 #

			ImproperlyConfigured at /

Module "django.core.context_processors" does not define a "auth" callable request processor

错误的原因： django.core.context_processors.auth 在1.2中使用， 但是到了django1.4， 这个东西改为了 django.contrib.auth.context_processors.auth 因此爆出了上面的错误。