开发环境的搭建
做K210的软件开发,有２种选择,
第一种方案是基于进行IDE开发,我们一般见过或者用过的IDE有visual studio,ecplise,keil,IAR等,对于嵌入式芯片k210,也有相应的ide,为该公司自己开发的,由于不成熟,在前期使用的过程中可能会出现一些错误,不过到现在这些错误大部分可以忽略,不影响大部分的开发,下面介绍K210 IDE的安装与使用．
安装：
首先打开github上kendryte-ide这个仓库网址,翻到下面的readme文件可以看到,ide的更新历史,从alpha到beta,alpha是刚刚开发成型,公司内部测试的;beta为大部分错误都已解决,还有一些错误放到市场上去测试检验;beta之后为release,release为正式发行的版本.
我们在下载的时候,点击branches,选择default branch进行下载,在命令行下解压,然后进入KendryteIDE文件夹,对于sh文件,需要有作一下操作,执行chmod +x KendryteIDE.sh,sh KendryteIDE.sh或者./KendryteIDE.sh.
之后会跳出一个界面,点击下方的Downloads,会进行自动的安装;在这之前我们需要下载一个代码编辑器visual studio code,下载参考https://blog.csdn.net/qq_34347375/article/details/80851417
在KendryteIDE同级目录中会出现kendryte-ide-development文件夹,要想启动该IDE,进入KendryteIDE目录,运行./KendryteIDE.sh,即可启动IDE.
基于IDE开发的demo,https://forum.kendryte.com/topic/125/%E5%A6%82%E4%BD%95%E7%94%A8ide%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AAhello-world%E9%A1%B9%E7%9B%AE



第2种方案就是基于gnu toolchain进行编译,一个c语言程序形成最终的可执行文件需要经历以下步骤,预处理,编译,汇编,链接.
预处理:展开头文件/宏替换/去掉注释/条件编译
编译:检查语法,生成汇编
汇编:汇编代码转换机器码
链接:链接到一起生成可执行程序

#编译前的准备
一般来说首先创建CMakeLists.txt;然后用cmake编译,生成Makefile;然后对Makefile进行make,最后对make生成的bin文件进行操作;在这个过程中我们需要注意这些文件的路径,一般来说CMakeLists.txt与源文件处于同一路径．
要完成上述的操作过程,需要安装好cmake,网上cmake的教程很多,但是大部分说的都不是很全,
官网下载安装包：https://cmake.org/download/
解压： tar -zxvf xx.tar.gz 
#将cmake添加进系统的环境变量
打开个人PATH路径配置,vim ~/.bashrc
在末尾添加:export PATH=/*/*/cmake-x-x/bin:$PATH
然后source ~/.bashrc
#距离cmake真正的能运行还有一段路
 执行以下命令,参考https://blog.csdn.net/qq_33278461/article/details/80414425

1）chmod +x ./booststrap
chmod +x ./configure
以下
2）./configure                #   ./configure是用来检测你的安装平台的目标特征的,比如它会检测你是不是有CC或GCC,并且会将shell文件生成makefile文件
   make all                   #   make是用来编译的,它从Makefile中读取指令,然后编译
   make install               #   make install是用来安装的,它从Makefile中读取指令,安装到指定位置
   cmake –version　

对于Linux操作系统中的PATH路径,https://blog.csdn.net/qq_33826564/article/details/82906115
在终端下运行echo $PATH,会出现一些目录,这就是系统的环境变量,当我们执行一些命令的时候,从PATH指定的路径(即系统搜索路径)下去搜索该命令,所以当我们把cmake加入到PATH路径下,我们运行的时候就是从系统搜索路径里去寻找,否则,可能就会出现寻找出错.

1.make是用来执行Makefile的
2.Makefile是类unix环境下类似于批处理的"脚本"文件,其基本语法是:目标+依赖+命令,只有在目标文件不存在时,或目标比依赖的文件更久,命令才会被执行.
3.makefile+make可理解为类unix环境下的项目管理工具,但它太基础了,抽象程度不高,于是就有了跨平台项目管理工具cmake
4.cmake是跨平台项目管理工具,他用更抽象的语法来组织项目．虽然,仍然是目标,依赖之类的东西,但更为抽象和友好,比如你用math表示数学库,而不需要再具体指定到底是math.dll还是libmath.so,在windows下它会支持生成visual studio的工程,在linux下它会生成makefile,甚至它还能生成eclipse工程文件.也就是说,从同一抽象规则出发,它为各个编译器定制工程文件
5.cmake是抽象层次更高的项目管理工具,cmake命令执行CMakeLists.txt文件
总结一下:make是用来执行Makefile，cmake用来执行CMakeLists.txt,cmake执行完CMakeLists.txt,会生成Makefile文件,需要make一下,在这里CMakeLists.txt比较好写,Makefile的比较抽象,难写.Makefile是定义编译规则、链接规则依赖关系的文件,可以看做是一系列的gcc/g++命令,但通常大项目各文件依赖关系复杂,makefile并不好写;而cmake则提供了自动构造makefile的一种工具,很方便．

要想完成一个程序的编译,还需要工具链,在Linux下一般采用GNU
GNU toolchain的安装
首先从github上下载Kendryte-gnu-toolchain,Kendryte-newlib
对于Kendryte-gnu-toolchain最主要的是要将其添加进PATH路径中去,推荐使用例程中的路径,/opt下
对于Kendryte-newlib按照github下的readme进行编译即可.

一般cmake出现错误都是路径问题,建议多试几次．


最后从github官网上下载kendryte-model-compiler,里面有一个How to Use Kendryte Model Compiler.md文件,按照那个步骤执行,即可.
   在cmake的时候可能会出现权限问题,需要我们跳转到root权限.
   以及编译器的问题,需要注意编译器是否正确安装,以及安装之后是否加入到PATH环境变量中去了.

#下载程序
如何将我们最后得到的目标程序,烧进开发板,结合kflash.py的README.md来看,需要注意的是,烧录的kflash.py需要与*.bin位于同一个目录.
