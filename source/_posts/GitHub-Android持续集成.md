---
title: GitHub-Android持续集成
date: 2016-09-03 15:13:22
tags: 打包方式
category: Android
---

# [Travis CI](https://travis-ci.org/)
Travis CI，是一个专门为开源项目打造的持续集成环境。
	
如果你有一个放在github上的开源项目，Travis CI简直就是一个完美的CI选择。

<!-- more -->

支持很多语言

	Android
	C
	C#
	C++
	Clojure
	Crystal
	D
	Dart
	Erlang
	Elixir
	F#
	Go
	Groovy
	Haskell
	Haxe
	Java
	JavaScript (with Node.js)
	Julia
	Objective-C
	Perl
	Perl6
	PHP
	Python
	R
	Ruby
	Rust
	Scala
	Smalltalk
	Visual Basic

[Android版 配置教程](https://docs.travis-ci.com/user/languages/android/)
## MAC 安装 Travis - CLI
Travis 是Ruby写的 所以安装吧...
首先需要安装Ruby, 你可以通过运行ruby -v 检查系统是否安装Ruby:

	#执行
	ruby -v
	
	#显示
	ruby 2.0.0p645 (2015-04-13 revision 50299) [universal.x86_64-darwin15]
	
	#执行
	gem install travis -v 1.8.0 --no-rdoc --no-ri
	
	#执行
	travis version
	#显示
	1.8.0
	
	提示： 诶 gem 安装比较慢 ，可能没反应，其实等会儿就好了。。。
	如果出现权限问题 gem install 前面 加sudo
 
## 开始集成
登陆网站 [travis-ci](https://travis-ci.org/)

### 登陆后

![tu](https://skyJinc.github.io/images/github集成测试/1.png)

### 同步

![tu](https://skyJinc.github.io/images/github集成测试/2.png)

### 开启

![tu](https://skyJinc.github.io/images/github集成测试/3.png)

### 创建  ***.travis.yml***
[Android配置教程](https://docs.travis-ci.com/user/languages/android/)


Travis的配置文件是使用 yml 一种标记语言编写的，如果有想了解的，可以自行搜索。

下面我们详细讲解上面脚本里面的元素都是代表什么意思。

#### 首先设置我们的语言，现在是Android应用

	language: android
	
#### 后面设置我们需用到Android里面相关的编译模块

	language: android   # 声明构建语言环境

	notifications:      # 每次构建的时候是否通知，如果不想收到通知邮箱
	  email: false
	
	sudo: false         # 开启基于容器的Travis CI任务，让编译效率更高。
	
	android:            # 配置信息
	  components:
	    - tools
	    - build-tools-23.0.2              
	    - android-23                     
	    - extra-android-m2repository     # Android Support Repository
	    - extra-android-support          # Support Library
	
	before_install:     
	 - chmod +x gradlew  # 改变gradlew的访问权限
	
	script:              # 执行:下面的命令
	  - ./gradlew assembleRelease  
	
	before_deploy:       # 部署之前
	  # 使用 mv 命令进行修改apk文件的名字
	  - mv app/build/outputs/apk/app-release.apk app/build/outputs/apk/buff.apk  
	
	deploy:              # 部署
	  provider: releases # 部署到GitHub Release，除此之外，Travis CI还支持发布到fir.im、AWS、Google App Engine等
	  api_key:           # 填写GitHub的token （Settings -> Personal access tokens -> Generate new token）
	    secure: 7f4dc45a19f742dce39cbe4d1e5852xxxxxxxxx 
	  file: app/build/outputs/apk/buff.apk   # 部署文件路径
	  skip_cleanup: true     # 设置为true以跳过清理,不然apk文件就会被清理
	  on:     # 发布时机           
	    tags: true       # tags设置为true表示只有在有tag的情况下才部署

#### 默认travis 会接受所有的 Licenses,但是你也可以定义自己的白名单
	language: android
	android:
	  components:
	    - build-tools-20.0.0
	    - android-L
	    - add-on
	    - extra
	  licenses:
	    - 'android-sdk-preview-license-52d11cd2'
	    - 'android-sdk-license-.+'
	    - 'google-gdk-license-.+'	
#### 添加模拟器

	# Emulator Management: Create, Start and Wait
	before_script:
	# 修改你的 gradlew的权限，默认gradlew没有权限执行的
	- chmod +x gradlew
	- echo no | android create avd --force -n test -t android-22 --abi armeabi-v7a
	- emulator -avd test -no-audio -no-window &
	- android-wait-for-emulator
	- adb shell input keyevent 82 &
#### 构建的时候，你肯定希望有一个干净的系统，所有需要清除缓存
	before_cache:
	- rm -f $HOME/.gradle/caches/modules-2/modules-2.lock
	cache:
	directories:
    - $HOME/.gradle/caches/
    - $HOME/.gradle/wrapper/

#### JDK版本
	jdk:
	- oraclejdk8
	- oraclejdk7
	- openjdk6

### [SkyApp项目](https://github.com/skyJinc/SkyApp)中完整配置

	language: android
	jdk: oraclejdk8
	sudo: false
	android:
	  components:
	  - platform-tools
	  - tools
	  - build-tools-24.0.0
	  - android-24
	  - android-22
	  - extra-android-m2repository
	  - sys-img-armeabi-v7a-android-22
	before_cache:
	- rm -f $HOME/.gradle/caches/modules-2/modules-2.lock
	cache:
	  directories:
	  - $HOME/.gradle/caches/
	  - $HOME/.gradle/wrapper/
	before_install:
	  - wget http://services.gradle.org/distributions/gradle-2.14-bin.zip
	  - unzip gradle-2.14-bin.zip
	  - export GRADLE_HOME=$PWD/gradle-2.14
	  - export PATH=$GRADLE_HOME/bin:$PATH
	  - gem install fir-cli
	
	after_deploy:
	  - fir p app/build/outputs/apk/app-release.apk -T $FIR_TOKEN -c "`git cat-file tag $TRAVIS_TAG`"
	
	
	script: gradle assembleRelease
	
	deploy:
	  provider: releases
	  api_key:
	    secure: Kjd3GltyVjkEaaToNY8L3JHzCSCfawpx1Mmd4vbbBmwVBVt3qBlDqRhEh8KYeZB1jeh935BxJetw6OAakJPrVbgOa2EjLRSfcAaxMo7AWDrrdoef4SZU8pPXCF9nJ0xoq4ysACPyabjukemsk8ydE4/b/4vjPt/z1eVBRzep00mLNqsCbWiNtp2B+xBzOu1ueHABjIjq25dKD2MFUUEJqKcaZNV8s3P0JZLZcyWnJ9MlZxCesbcDFacCfDFg6SzlD8LahuSRmeH8qnw8vLRMitrNV/o5YcSn5a6ygOJaI9gYp8X3sVVjIPCRtPuBRR7XMVEhzXVfRbPsKbU4xxp7Zvmxz17HVyCkRLq6Qulz4nQ2twcepElxnTPIVWyATz97ZCmCfsGaSUqiZBu5LHGCUPOfsU/oD7seMhJMzaEg8g2N9ySv2Mgp8ZRmr28zsB53gCHzOty3FNVh4FkszsJ1mXUfj8ZF9DpUdJt90vL/nWVy8x4BZ0i5LoMCVUGA2PtEVyVsziM62kVxFhd7tFYwvpXAzChgsxOZBQKwO2cW3Pr6VE/6j9dFqy6teI8jrsas6oc9R1TQ6YFFmVw1RU4cCJuujqsAzW3T/LLviHvCO6Pow8v9RZS0fRqzhJLAeDAtAfjfsfvzT/6S8fCKDjVtUn44kbjvMi2MIh5f2nZjSUo=
	  file: app/build/outputs/apk/app-release.apk
	  skip_cleanup: true
	  on:
	    repo: skyJinc/SkyApp
	  

### 查看测试结果
- 执行***gradle build connectedCheck*** 

- 提交到github后 会自动开始进行测试

![tu](https://skyJinc.github.io/images/github集成测试/4.png)
![tu](https://skyJinc.github.io/images/github集成测试/5.png)

### 权限问题

- 测试结果如果类似以下配置

![tu](https://skyJinc.github.io/images/github集成测试/6.png)

按照提示 ***.travis.yml*** 里增加 

	sudo: required

![tu](https://skyJinc.github.io/images/github集成测试/7.png)

### Gradle问题
#### 项目中自带gradle
![tu](https://skyJinc.github.io/images/github集成测试/8.png)

这个时候需要权限 所以 要加载

	before_install:
		#- chmod +x gradlew

#### 项目中不带gradle
因为我在SkyApp项目中没有添加 gradle ，采用了本地Gradle方式 所以 需要指定 ***Travis*** 的gradle版本

	before_install:
		#- chmod +x gradlew
		- wget http://services.gradle.org/distributions/gradle-2.14-bin.zip
		- unzip gradle-2.14-bin.zip
		- export GRADLE_HOME=$PWD/gradle-2.14
		- export PATH=$GRADLE_HOME/bin:$PATH

SkyApp使用了Gradle2.14

提交到github后, 重新进行测试


## 发布

### Travis Ci自动发布安装apk文件到Github Release

Travis CI的script部分运行成功后，可以通过配置文件进入到发布阶段。下面是一个Travis CI发布的示例

	deploy:
	  provider: releases
	  user: "GITHUB USERNAME"
	  password: "GITHUB PASSWORD"
	  file: app/build/outputs/apk/app-release.apk
	  skip_cleanup: true
	  on:
	  	tags: true
 - provider：发布目标为Github Release
 - Github用户名和密码，因为Travis CI要上传APK文件，因此需要有Github项目的写入权限
 - file： 发布文件，输入文件路径即可
 - skip_cleanup: 默认情况下Travis CI在完成编译后会清除所有生成的文件，因此需要将skip_cleanup设置为true来忽略此操作。
 - on： 发布的时机，这里配置为tags: true，即只在有tag的情况下才发布。

这边直接暴露了Github密码是我们更加不能接受的。更好的做法是在Github -> settings -> Personal access tokens 生成一个只能访问当前项目并只有读取权限的Github Access Token，并通过Travis CI将Access Token加密。好在Travis CLI中已经可以通过一行指令做好这一切：

	#第一步安装已经完毕后，进入SkyApp 执行以下命令
	travis setup release

根据提示填写上述配置项目的信息后，Travis CLI会自动在.travis.yml文件中生成好所有的配置项：

	deploy:
	  provider: releases
	  api_key:
	    secure: Kjd3GltyVjkEaaToNY8L3JHzCSCfawpx1Mmd4vbbBmwVBVt3qBlDqRhEh8KYeZB1jeh935BxJetw6OAakJPrVbgOa2EjLRSfcAaxMo7AWDrrdoef4SZU8pPXCF9nJ0xoq4ysACPyabjukemsk8ydE4/b/4vjPt/z1eVBRzep00mLNqsCbWiNtp2B+xBzOu1ueHABjIjq25dKD2MFUUEJqKcaZNV8s3P0JZLZcyWnJ9MlZxCesbcDFacCfDFg6SzlD8LahuSRmeH8qnw8vLRMitrNV/o5YcSn5a6ygOJaI9gYp8X3sVVjIPCRtPuBRR7XMVEhzXVfRbPsKbU4xxp7Zvmxz17HVyCkRLq6Qulz4nQ2twcepElxnTPIVWyATz97ZCmCfsGaSUqiZBu5LHGCUPOfsU/oD7seMhJMzaEg8g2N9ySv2Mgp8ZRmr28zsB53gCHzOty3FNVh4FkszsJ1mXUfj8ZF9DpUdJt90vL/nWVy8x4BZ0i5LoMCVUGA2PtEVyVsziM62kVxFhd7tFYwvpXAzChgsxOZBQKwO2cW3Pr6VE/6j9dFqy6teI8jrsas6oc9R1TQ6YFFmVw1RU4cCJuujqsAzW3T/LLviHvCO6Pow8v9RZS0fRqzhJLAeDAtAfjfsfvzT/6S8fCKDjVtUn44kbjvMi2MIh5f2nZjSUo=
	  file: app/build/outputs/apk/app-release.apk
	  on:
	    repo: skyJinc/SkyApp
	 
### 自动发布安装apk文件到fir.im

#### 登陆fir.im 复制api

![tu](https://skyJinc.github.io/images/github集成测试/9.png)
![tu](https://skyJinc.github.io/images/github集成测试/10.png)

#### 到travis -》 你的项目 进行设置

![tu](https://skyJinc.github.io/images/github集成测试/11.png)
 
#### .travis.yml 里添加 以下代码


	before_install:
		- gem install fir-cli
	after_deploy:
		- fir p app/build/outputs/apk/app-release.apk -T $FIR_TOKEN -c "`git cat-file tag $TRAVIS_TAG`"


 注意: FIR_TOKEN 是你在 travis 添加的key
 
#### 发布以后的结果
GitHub

![tu](https://skyJinc.github.io/images/github集成测试/12.png)

Fir.im
 
![tu](https://skyJinc.github.io/images/github集成测试/13.png)
 
## 问题

如果有问题, 留言提给我。 我会随时查看帮你解答

