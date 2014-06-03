---
layout: post
permalink: /error-about-install-pymc-on-windows-xp
title: pymc在windows上安装报错
---

# pymc在windows上安装报错 #


1. 使用easy_install pymc. 装好了之后

    >easy_install pymc

    Searching for pymc

    Reading http://pypi.python.org/simple/pymc/

    Reading http://pypi.python.org/simple/pymc/pymc.googlecode.com

    Reading http://github.com/pymc-devs/pymc

    Best match: pymc 2.2

    Downloading http://pypi.python.org/packages/2.7/p/pymc/pymc-2.2-py2.7-win32.egg#

    md5=789c865d6c05fa2bd523ca209894aa97

    Processing pymc-2.2-py2.7-win32.egg

    creating c:\python27\lib\site-packages\pymc-2.2-py2.7-win32.egg

    Extracting pymc-2.2-py2.7-win32.egg to c:\python27\lib\site-packages

    Adding pymc 2.2 to easy-install.pth file



    Installed c:\python27\lib\site-packages\pymc-2.2-py2.7-win32.egg

    Processing dependencies for pymc

    Finished processing dependencies for pymc


我提前装了numpy的..

然后, 就开始import pymc

    >>> import pymc



    Traceback (most recent call last):

      File "<stdin>", line 1, in <module>

      File "C:\Python27\lib\site-packages\pymc-2.2-py2.7-win32.egg\pymc\__init__.py"

    , line 25, in <module>

        from .Container import *

      File "C:\Python27\lib\site-packages\pymc-2.2-py2.7-win32.egg\pymc\Container.py

    ", line 41, in <module>

        from pymc.Container_values import LCValue, DCValue, ACValue, OCValue

    ImportError: DLL load failed: 找不到指定的模块。

上网找到 2个资料:  https://groups.google.com/forum/?fromgroups=#!topic/pymc/x6ghi0frOhI
和 https://github.com/pymc-devs/pymc/issues/61]

原来pymc使用了c语言模块和fortran模块, 并且是源码级别的. 如果没有别人在windows上编译好的静态包, 想在windows上安装pymc是不太容易的.

按照google上的那个文档来说, 有可能是少了2个dll: libgcc_s_dw2-1.dll, and flib.pyd depends
on libgftran-3.dll.

另外源码安装的话, 应该先安装2个编译器, 一个就是windows上的 c 编译器, 一般使用MinGW32, 另外一个就是Fortran 编译器.

可惜两个我都不是很熟悉. 因为这都是从linux系统上面移植到windows上的工具.

有了mingw32和fortran编译器, 安装pymc使用下面2行:

    python setup.py build --force -c mingw32

    python setup.py install --force --skip-build
    实际上我没装fortran编译器,最后提示说xxx文件是fortran, 需要一个fortran compiler.

后面的以后有时间再弄.

后来我安装了tdm-gcc-4.7.1  还是需要fortran compiler, 然后我上网搜索了fortran的编译器, 但是还是想不要太麻烦, 我想mingw应该不会傻到不自带fortran的编译器吧. 突然想起安装 tdm-gcc的最开始有个manage的按钮, 我认为这里可以管理mingw的包, 于是重新安装tdm-gcc, 点manage进入安装, 步骤跟点create new差不多, 最后在配置选择那里点开mingw的树形选择, 发现真的有fortran的支持, 只是前面没有打勾, 也就是说默认状态下, 是不带fortran编译器的. 然后我打上勾, 继续, 开始从网上自动下载fortran的安装包了.

装好了之后, 继续使用命令:

python setup.py build --force -c mingw32

然后出现的错误如下:

    compiling Fortran sources

    Fortran f77 compiler: C:\MinGW32\bin\gfortran.exe -Wall -ffixed-form -fno-second

    -underscore -mno-cygwin -O3 -funroll-loops

    Fortran f90 compiler: C:\MinGW32\bin\gfortran.exe -Wall -fno-second-underscore -

    mno-cygwin -O3 -funroll-loops

    Fortran fix compiler: C:\MinGW32\bin\gfortran.exe -Wall -ffixed-form -fno-second

    -underscore -mno-cygwin -Wall -fno-second-underscore -mno-cygwin -O3 -funroll-lo

    ops

    compile options: '-Ibuild\src.win32-2.7 -IC:\Python27\lib\site-packages\numpy-1.

    6.2-py2.7-win32.egg\numpy\core\include -IC:\Python27\include -IC:\Python27\PC -c

    '

    gfortran.exe:f77: pymc\flib.f

    gfortran.exe: error: unrecognized command line option '-mno-cygwin'

    error: Command "C:\MinGW32\bin\gfortran.exe -Wall -ffixed-form -fno-second-under

    score -mno-cygwin -O3 -funroll-loops -Ibuild\src.win32-2.7 -IC:\Python27\lib\sit

    e-packages\numpy-1.6.2-py2.7-win32.egg\numpy\core\include -IC:\Python27\include

    -IC:\Python27\PC -c -c pymc\flib.f -o build\temp.win32-2.7\Release\pymc\flib.o"

    failed with exit status 1

真的很令人恼火, 编译fortran源代码的时候居然出现一个 -mno-cygwin的选项, 难道要我再安装cygwin吗? 搜索 fortran -nmo-cygwin 没搜到什么东西, 就搜索 nmo-cygwin, 发现stackoverflow上有这一篇: http://stackoverflow.com/questions/9645004/python-mno-cygwin] 和这一篇 http://stackoverflow.com/questions/6034390/compiling-with-cython-and-mingw-produces-gcc-error-unrecognized-command-line-o]

说是: 新版本的gcc 4.7.0 以后的版本, 移除了 -nmo-cygwin这个选项, 解决的办法就是:

方法1. 使用老一点的gcc版本来编译

方法2. 把python目录下的 C:\Python27\Lib\distutils\cygwinccompiler.py 修改一下. 把文件中的 -mno-cygwin 全部删掉.

2.1. 如果有可能还出错的话, 好像还需要修改 上面那个文件中的 get_msvcr() 函数, 当 msc_ver == '1500' 的时候, 不要返回 ['msvcr90'], 修改为返回空列表, [''].

按照上面的试了方法2,(2.1没试), 结果还是不行

    compiling Fortran sources

    Fortran f77 compiler: C:\MinGW32\bin\gfortran.exe -Wall -ffixed-form -fno-second

    -underscore -mno-cygwin -O3 -funroll-loops

    Fortran f90 compiler: C:\MinGW32\bin\gfortran.exe -Wall -fno-second-underscore -

    mno-cygwin -O3 -funroll-loops

    Fortran fix compiler: C:\MinGW32\bin\gfortran.exe -Wall -ffixed-form -fno-second

    -underscore -mno-cygwin -Wall -fno-second-underscore -mno-cygwin -O3 -funroll-lo

    ops

    compile options: '-Ibuild\src.win32-2.7 -IC:\Python27\lib\site-packages\numpy-1.

    6.2-py2.7-win32.egg\numpy\core\include -IC:\Python27\include -IC:\Python27\PC -c

    '

    gfortran.exe:f77: pymc\flib.f

    gfortran.exe: error: unrecognized command line option '-mno-cygwin'

    error: Command "C:\MinGW32\bin\gfortran.exe -Wall -ffixed-form -fno-second-under

    score -mno-cygwin -O3 -funroll-loops -Ibuild\src.win32-2.7 -IC:\Python27\lib\sit

    e-packages\numpy-1.6.2-py2.7-win32.egg\numpy\core\include -IC:\Python27\include

    -IC:\Python27\PC -c -c pymc\flib.f -o build\temp.win32-2.7\Release\pymc\flib.o"

    failed with exit status 1


 然后就在python的安装目录中找 -nmo-cygwin, 结果在numpy的模块目录下找到了2个文件包含 了 -nmo-cygwin , mingw32compiler.py 和 gnu.py , 首先尝试去掉 gnu.py 中的 -nmo-cygwin , 86-89 行注释掉,

    # use -mno-cygwin for g77 when Python is not Cygwin-Python

        #if sys.platform == 'win32':

        #    for key in ['version_cmd', 'compiler_f77', 'linker_so', 'linker_exe']:

        #        executables[key].append('-mno-cygwin')

结果还是不行.

然后又把237-239行注释掉, 加个pass, 最后出现了 running scons. 没报错, 也不知道 running scons是什么错误.:


    if sys.platform == 'win32':

                    #for key in ['version_cmd', 'compiler_f77', 'compiler_f90',

                    #            'compiler_fix', 'linker_so', 'linker_exe']:

                    #    self.executables[key].append('-mno-cygwin')

                    pass



既然没什么错误了, 我就继续执行:

setup.py install --force --skip-build

出现如下错误:

    running install_egg_info

    Writing C:\Python27\Lib\site-packages\pymc-2.2-py2.7.egg-info

    running install_clib

    No module named msvccompiler in numpy.distutils; trying from distutils

    customize MSVCCompiler