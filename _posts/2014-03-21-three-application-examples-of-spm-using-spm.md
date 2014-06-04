---
layout: post
permalink: /three-application-examples-of-spm-using-spm
title: spm使用之三spm应用实例
---

spm 的init实际上是调用了grunt这个工具来实现一些交互式的提问和数据的获取.

看看npm就知道, npm有个命令叫init, 就是一样的交互式提问获取你要创建的nodejs的模块信息.

spm从这个角度一看就是抄袭npm的方式, 当然, 说抄袭难听了点, 叫借鉴吧, 也是, 秀才摸得, 我和尚难道摸不得?

spm init 这个命令, 实际上就是将 C:\Documents and Settings\Administrator\.spm\init\cmd\root 和 C:\Documents and Settings\Administrator\.spm\themes\cmd 文件夹下的文件, 通过在一些模板文件中替换掉一些在交互阶段得到的参数, 然后复制到你执行spm init 命令的模块目录里, 比如D:\projects\box 等等.

首先谈, 这个css和js引用的问题, 既然都已经用上seajs了, 那么最好还是到https://github.com/seajs/examples 下载几个例子下来学习.

解压缩 examples 到D:\Projects\. 然后进入D:\Projects\examples-master目录, 会发现如下目录结构:

    .gitignore
    app/
        index.html
        README.md
    sea-modules/
    static/

进入sea-modules, 有如下目录:

    angular/
    examples/
    gallery/
    jquery/
    seajs/


再进入examples目录, 这里有hello, lucky等等seajs模块.



在这个目录下, 我们故伎重施, 还是重新创建box

    md box
    cd box
    spm init
    #挨个回答问题
    ...
    #完了之后
    spm doc watch 直接运行在本机8000端口




然后修改readme.md, 加入中文, 把文件转换为utf-8编码&#26684;式. 另外, js文件意思一样, 如果有中文, 都转换为utf-8编码&#26684;式.

好了, 下面就是应用的实例了.


实例从哪里来? 还是从  高富帅seajs使用示例及spm合并压缩工具露脸 http://www.zhangxinxu.com/wordpress/2012/07/seajs-node-nodejs-spm-npm/ 这篇文章中来吧.

因为作者个博客虽然介绍了怎么使用seajs, 但是没有同时介绍怎么使用spm来管理自己写的这个模块, 为了省事, 我把作者的这几个例子再演示一遍. 当然了, 东西还是放在我自己刚弄的box模块里. 我弄这个的主要目的是结合seajs来介绍spm的doc

1.半透明背景遮罩层

进入box模块, 打开src目录, 新建zhezhao.js, 名字就不叫overlay了, 因为overlay好像和seajs现有的一个模块overlay同名, 每次在调试的时候,我都怀疑seajs并没有加载我的overlay.js, 而是加载了seajs/还是aliceui?自己提供的overlay.js, 因为我在chrome调试的时候, 没有看到对我本地的overlay.js的http请求, 也许是没请求这个文件.

内容直接复制如下:

    define(function(require, exports, module) {
      // 模块代码
    });



然后再创建一个 elementCreate.js 的JavaScript文件，同样的套用，如下JavaScript代码:


    /**
     * 创建元素
     */

    define(function(require, exports) {
        exports.create = function(tagName, attr) {
            var element = null;
            if (typeof tagName === "string") {
                element = document.createElement(tagName);

                if (typeof attr === "object") {
                    var keyAttr, keyStyle;
                    for (keyAttr in attr) {
                        if (keyAttr === "styles" && typeof attr[keyAttr] === "object") {
                            // 样式们
                            for (keyStyle in attr[keyAttr]) {
                                element.style[keyStyle]    = attr[keyAttr][keyStyle];

                                if (keyStyle === "opacity" && window.innerWidth + "" == "undefined") {
                                    element.style.filter = "alpha(opacity="+ (attr[keyAttr][keyStyle] * 100) +")";
                                }
                            }
                        } else {
                            if (keyAttr === "class") {
                                keyAttr = "className";
                            }
                            element[keyAttr] = attr["class"];
                        }

                    }
                }
            }
            return element;
        };
    });


注意, 最上面有中文, 所以, 最好马上转换为 utf-8编码&#26684;式.

回到zhezhao.js文件, 全部内容修改为:


    /**
     * 黑色半透明遮罩层
     */

    define(function(require, exports, module) {
        var elementCreate = require("./elementCreate");
        var tanchu = (function() {
            var element = elementCreate.create("div", {
                styles: {
                    display: "none",
                    width: "100%",
                    backgroundColor: "#000",
                    opacity: 0.35,
                    position: "absolute",
                    zIndex: 1,
                    left: 0,
                    top: 0,
                    bottom: 0
                }
            });
            document.body.appendChild(element);

            return {
                display: false,
                show: function() {
                    element.style.display = "block";
                    this.display = true;
                    return this;
                },
                hide: function() {
                    element.style.display = "none";
                    this.display = false;
                    return this;
                }
            };
        })();

        exports.zhezhao=zhezhao
    });


同样转为utf-8编码&#26684;式.

然后就是演示这个半透明遮罩层了, 这个主要是用在弹出框的model对话框的背景用的.

好戏开始了, 修改box目录里的readme.md, 为什么要修改这呢? 因为这个才是spm doc的出彩之处, 在一对4个````符号的包裹下, spm doc 会将markdown&#26684;式的readme.md文件中的 ```` 代码 ````转换成2部分, 一部分就是你在html中看到的代码, 另一部分直接转换成html的一部分了, 所以才精彩, 好了, 修改readme.md 如下:

    # box

    ---

    带边框和标题的标准区块。

    ---

    ## 演示文档

    <link type="text/css" rel="stylesheet" media="screen" href="src/box.css">

    ### 默认用法

    ````html
    <div class="ui-box">
        <div class="ui-box-head">
            <div class="ui-box-head-border">
                <h3 class="ui-box-head-title">区块标题</h3>
            </div>
        </div>
        <div class="ui-box-container"></div>
    </div>
    ````

    ---

    ## Usage

    It is very easy to use this module.

    ````html
    <div class="alice-box">
    <button id="button">点击toggle</button>
    </div>
    ````

    ```javascript
    var myOverlay = null;
    seajs.use("./zhezhao", function(zhezhao) {
    	myOverlay = zhezhao.zhezhao;
    });

    document.getElementById("button").onclick = function() {
    	myOverlay[myOverlay.display? "hide": "show"]();
    };
    ```

    ## Api等等

    Here is some details.细节



解释, ````html 表示是html, 下一行的内容直到````结束, 会同时显示代码和转换成真正的html

````javascript 后面的内容会显示为javascript( 重新编码过, 显示在html中, 并且会转换成html文件里真正可用的script, 两头加上<script>...</script>标签

解释完毕

把上面的内容保存, 并且转换为utf-8编码&#26684;式.

然后, 就是见证奇迹的时刻了. 噔噔噔, 刷新http://localhost:8000/     然后会看到一个按钮







点击 toggle, 然后应该会有一个暗灰色的遮罩层.

结果是, 妈蛋, 点击了之后, 没有任何反应!!!!

这个时候, 真的是天怒人怨的时刻到了!

我查找了很多地方, 居然没有任何人解释这个问题, 甚至翻遍了spm自带的模板, 甚至想研究一下狗日的spm doc到底是怎么转换md文件中的css和js代码的, 无奈水平有限, 没找到, 但是让我发现 spm 自带的模板是可以修改的, 这个下回再说, 这里先解决这个javascript代码为什么没有自动转换为html可以用的代码这个问题.

相信你们也找不到答案, 所以我就不卖关子, 直接说了, 答案就是, box/readme.md中的javascript代码, spm doc不会转换它, 他只会转换box/examples/index.md 里的, 或者说是转换 box/examples/   目录下的*.md文件中的javascript为可以使用的javascript, 这是我无意中发现的.

因此, 把上面的一段代码弄到 box/examples/index.md 先试试:


    # Demo

    ---

    ## Normal usage

    ````html
    <div class="alice-box">
    <button id="button">点击toggle</button>
    </div>
    ````

    ```javascript
    var myOverlay = null;
    seajs.use("./zhezhao", function(zhezhao) {
    	myOverlay = zhezhao.zhezhao;
    });

    document.getElementById("button").onclick = function() {
    	myOverlay[myOverlay.display? "hide": "show"]();
    };
    ```

然后保存并转换为utf-8编码&#26684;式, 接着, http://localhost:8000/ 这里的左边点击"演示", 就会进入到http://localhost:8000/examples/index.html

这个时候, 再点击toggle, 就会出现半透明遮罩层了.

实际上, 这次写这个文件的时候, 连box/examples/index.md 转换为 index.html的时候, 都没有正确的解析出html中可用的 javascript, 导致我怀疑我上次在examples目录中这么弄, 弄好了, 是不是在梦里实现的.

&#20540;得一提的是上次弄好的, 是因为俩个小地方,

1 是直接创建了zhezhao这个模块,  直接创建overlay好像不能加载overlay, 不知道是不是和现有的seajs模块同名的原因.

2 是将seajs.use('./zhezhao') 和seajs.use('zhezhao') , 来回切换, 终于是调试好了.

有时候, 你会想用seajs.use('http://localhost:8000/src/zhezhao')这种来尝试, 最关键的还是看html源码, 看看你的javascript是不是转义了, 按照spm doc的正确解析, 应该html中会有你在文档中写的javascript代码的2个形式, 一个是转义后的js, 另一个就是套了<script>标签的可以用的js. 只要这个出现了, 那么, 在完成你写好的模块的使用文档的同时, 你的演示用的例子也做好了.

所以, 这个才是spm管理模块的出色的地方, 也是我最喜欢的部分, 其余spm build , 创建压缩js, css文件等等, 已经不新鲜了, 不&#20540;得我再去为这个写点什么了.

------------------------------------

问题终于发现了, 是我自己上面没有按照4个````js代码的形式```` 来写这些js代码, 我只写了3个```, 因此, 错误怎么都找不到...

当然, 这也是今天的意外发现, 因为前几天没发现3个```的东西就只转义显示js, 不出现js. 而4个````就会同时出现你写的js代码的2个形式, 上面说了的.

这个问题解决之后, 我发现, 不仅仅 在演示页的 点击toggle可以用了, 连box首页的 点击 toggle也可以用了.

如下图:



有一个问题就是这个toggle按钮, 点击之后, 就跑到遮罩层的下面去了, 想再次点击, 就怎么都点不了了. 方法就是设置他的css,, 让他的z-index比遮罩层的z-index的&#20540;要大,

这里设置为3就行了.

打开, src/box.css

把这一句加上:

button{margin:1em; position:relative; z-index:3;}

保存,刷新,即可

其他的就不多写了.

反正spm配合seajs写模块的步骤就是:

md box

cd box

spm init

然后进入 box/src 文件夹, 按照CMD规范写好seajs模块

然后在readme.md中写好你的模块的文档, js用4个````符号包裹,  然后使用spm doc watch来查看和调试, 就可以再完成文档的同时, 也完成了示例. 一箭双雕, 还是很爽的.

当然, 我还有其他的例子, 还有spm模板的修改, 还有spm 结合aliceui来弄东西没有写, 而aliceui才是我最想关注的东西. 下回再续吧.