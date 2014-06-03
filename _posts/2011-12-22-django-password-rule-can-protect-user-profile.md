---
layout: post
permalink: /django-password-rule-can-protect-user-profile
title: Django的密码机制能克服CSDN泄露门吗
---

# Django的密码机制能克服CSDN泄露门吗 #



	12月21日，开发者技术社区CSDN的用户数据库被泄露，众多用户账号密码赤裸裸地被公开，立即引发了互联网业界一片哗然。此后，陆续又有多家公司的账号被泄露，在这其中，安全软件公司金山毒霸的用户数据库赫然在列。另有媒体报道，向外界公开CSDN数据库的，竟是金山内部员工！



	CSDN的600万用户数据库泄漏后，CSDN已证实此消息并报案。



	那么我们用django建站的话，django的密码机制能不能保护我们用户的资料呢？



	django的密码加密机制需要从django 的源码中找端倪。django的加密，现在用的sha1，还有一段是md5。



	说说思路，我们可以从数据库种看到用户的密码加密后的密文。类似：sha1$2019b$05f132b2c8ae83b6556ba887a6e7b0b320dee917


    def set_password(self, raw_password):

        import random

        algo = ’sha1′

        salt = get_hexdigest(algo, str(random.random()), str(random.random()))[:5]

        hsh = get_hexdigest(algo, salt, raw_password)

        self.password = ‘%s$%s$%s’ % (algo, salt, hsh)





	可以看到所有的密码都有3部分组成，由$隔开，依次是算法，盐，密文。

	算法是固定的，盐是随机的，密文是根据随机的盐生成的。

	下面看看


    def get_hexdigest(algorithm, salt, raw_password):

    """

    Returns a string of the hexdigest of the given plaintext password and salt

    using the given algorithm (‘md5′, ’sha1′ or ‘crypt’).

    """raw_password, salt = smart_str(raw_password), smart_str(salt)

    if algorithm == ‘crypt’:

    try:

    import crypt

    except ImportError:

    raise ValueError(‘”crypt” password algorithm not supported in this environment’)

    return crypt.crypt(raw_password, salt)

    if algorithm == ‘md5′:

    return md5_constructor(salt + raw_password).hexdigest()

    elif algorithm == ’sha1′:

    return sha_constructor(salt + raw_password).hexdigest()#注意这行，就是接受string生成密文，

    raise ValueError(“Got unknown password algorithm type in password.”)





	所以：算法是sha1，盐是2个随机字符串相加根据sha1生成的密文的前五位，密文是盐和用户密码相加根据sha1生成的密文。



	总体来说，django的密码机制目前相比MD5来说，已经有了一个进步了。应该说比md5加密要更安全。目前网上巨大的md5比对数据库，已经庞大到足以破解一个拥有10万-100万用户的网站了，这个信息在微博上可以找到，所以，md5就安全的想法是不靠谱的。相反，目前django的sha1加密混合MD5的加密方法，还是比MD5要安全很多。