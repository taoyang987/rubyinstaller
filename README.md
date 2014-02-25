# windows下安装ruby2.1.1 (i386 && x86_64)

**Note:**  像扶桑妹纸致敬！＼（￣︶￣）／  

## 先决条件
- 本地 **有可用ruby** 做bootstrap
- bootstrap ruby gem list **必须有rb-readline**，建议0.5.0+
- 有git和bash环境可以fork源码
- 可以翻墙；部分下载下不动的，需要手动set|export http|https proxy

## 源码编译ruby和devkit到sandbox
```bash
git clone https://github.com/cclu/rubyinstaller.git
cd rubyinstaller
rake devkit:ls
rake ruby21 openssl=1.0.1l libffi=3.0.13 rubygems=2.2.0
```
这里默认使用的是mingw4.8.1分支；如果需要更改分支，构建的时候可以跟上参数：
```bash
rake ruby21 dkver=mingwbuilds-32-4.9.0
```  

构建完毕。  

## 安装ruby
直接复制几个文件即可，这里以D盘为例（需要自建目录）：
```bash
cp -r sandbox/ruby21_mingw/* /d/ruby211
cp -r sandbox/devkit /d/ruby210/devkit
```
再把 D:\ruby211\devkit\mingw\bin;D:\ruby211\bin; 写入环境变量即可  

测试：
```bat
CMD> ruby -v
ruby 2.1.1p76 (2014-02-24 revision 45161) [i386-mingw32]

CMD> gem -v
2.2.2

CMD> gem list

*** LOCAL GEMS ***

bigdecimal (1.2.4)
io-console (0.4.2)
json (1.8.1)
minitest (4.7.5)
psych (2.0.3)
rake (10.1.0)
rdoc (4.1.0)
test-unit (2.1.1.0)
```

## 安装native c gem  
这里以mysql为例。首先从龟壳下载并解压mysql-connector-c（或者noinstall），
然后去devkit中打开mingw-msys，输入：
```bash
$ gem install mysql2 --no-ri --no-rdoc -- --with-mysql-dir=/d/MySQL/mysql-connector-c-6.1.3-win32/
Fetching: mysql2-0.3.15.gem (100%)
Building native extensions with: '--with-mysql-dir=d:/MySQL/mysql-connector-c-6.1.3-win32/'
This could take a while...
Successfully installed mysql2-0.3.15
1 gem installed
```
x64需用x64 connector c  

再打开cmd，用irb测试：
```bat
CMD> irb
DL is deprecated, please use Fiddle
irb(main):001:0> require 'mysql2'
=> true
irb(main):002:0>
```

结束……换地方去the villar club。

*****
### 后记
rubyinstaller x86_64，就是专门挖坑的。他采用mingw和mingw64专用的x64 gcc编译，
但是采用32为的msys，而不是msys64。所以其x64的配置文件都在compilers/mingw64.rb下，
和mingwbuilds.rb的区别是，需要通过compiler.host指定configure --host。  
rubyinstaller所用的mingw和mingw64，均为sourceforge上的personal build，
需要你对mingw系列比较熟，有一定经验，能够找到相关资源；否则想自己升级是非常困难的。  
费了半天事，老娘才找齐x86_64下能用的稳定版资源。  
通过下述命令可以编译出ruby211p76的64位可执行文件（**上次编译的sandbox目录必须干掉**）：  
```bash
rm -rf sandbox
rake ruby21 openssl=1.0.1l libffi=3.0.13 rubygems=2.2.0 dkver=mingw64-64-4.8.0
CMD> ruby -v
ruby 2.1.1p76 (2014-02-24 revision 45161) [x64-mingw32]
```
还不如cygwin呢 (⊙ˍ⊙)  

*****
cygwin步骤：  
* readline openssl等库自己补上~~  

* 编译libffi：  
```bash
#!/usr/bin/env bash

make clean 2>&1

./configure --enable-shared > c.log 2>&1
make  > m.log 2>&1
make install  > i.log 2>&1
```

* 编译libyaml：  
```bash
#!/usr/bin/env bash

make clean 2>&1

./configure CFLAGS="-DYAML_DECLARE_STATIC" > c.log 2>&1
make > m.log 2>&1
make install > i.log 2>&1
```

* 编译ruby：
```bash
#!/usr/bin/env bash

export CC=/usr/local/llvm34/bin/clang
export CXX=/usr/local/llvm34/bin/clang++
export CFLAGS=-Qunused-arguments
export CPPFLAGS=-Qunused-arguments

./configure --prefix=/usr/local/llvm_ruby210 --with-opt-dir=/usr/local --with-out-ext=tk > c.log 2>&1
make > m.log 2>&1
make install > i.log 2>&1
```
和Mac下脚本相同。才不去折腾rvm呢……