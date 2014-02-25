# windows-x86下安装ruby2.1.1

**Note:**  像扶桑妹纸致敬！  

## 先决条件
- 本地有可用ruby做bootstrap
- bootstrap ruby gem list必须有rb-readline，建议0.5.0+
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
rake ruby21 dkver=mingwbuilds-32-4.8.2
```  

构建完毕。  

## 安装ruby
直接复制几个文件即可，这里以D盘为例（需要自建目录）：
```bash
cp -r sandbox/ruby21_mingw/* /d/ruby210
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