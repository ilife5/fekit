FEKIT
=====================

## fekit是什么? ##

##### fekit是一套前端开发工具, 其中包含了
* 本地开发支持环境
* 静态文件编译 css / js
* 开发辅助工具等

## 如何安装 ##

#### 安装前提

##### nodejs & npm
* 版本需大于 0.6
* windows: [http://nodejs.org/dist/v0.8.15/node-v0.8.15-x86.msi](http://nodejs.org/dist/v0.8.15/node-v0.8.15-x86.msi)
* mac: [http://nodejs.org/dist/v0.8.15/node-v0.8.15.pkg](http://nodejs.org/dist/v0.8.15/node-v0.8.15.pkg)
* linux: 自行使用 apt-get(ubuntu) 或 yum(centos) 安装

##### svn
* windows: 使用Slik SVN, [http://www.sliksvn.com/en/download](http://www.sliksvn.com/pub/Slik-Subversion-1.7.7-win32.msi)
* mac: 自带 或使用 port 安装
* linux: 自行使用 apt-get(ubuntu) 或 yum(centos) 安装

##### rsync(可选) #####
* 如不安装, 可能会影响 sync 命令的使用 
* windows: [http://optics.ph.unimelb.edu.au/help/rsync/rsync_pc1.html#install](http://optics.ph.unimelb.edu.au/help/rsync/rsync_pc1.html#install)
* mac: 自带
* linux: 自带
* 请自行配置免密码ssh登录

#### 安装
    
    npm install fekit -g

### 使用

    fekit {命令名} --help 

### fekit.config

    {
        // 编译方案, 参考 [issue #1](https://github.com/rinh/fekit/issues/1)
        "compiler" : false 或 "modular" ,

        // 库的配置, 该库作为编译时, @import url 和 require 使用
        "lib" : {
            "core" : "./src/core"  /* 该路径相对于当前fekit.config文件 */
        } ,

        // 将要导出至 `prd` 和 `dev` 目录的文件列表
        // 其中所有路径, 均相对于 `src` 目录
        "export" : [

            // 第一种配置方式, 直接写出要导出的文件相对路径
            "./scripts/page-a.js" ,   

            // 第二种配置方式, 当要导出的文件, 在实际使用时有上级依赖, 则可以将上级依赖的文件加入`parents`节点
            { 
                "path" : "./scripts/page-b.js" ,
                "parents" : [ "./scripts/page-a.js" ]
            } , 

            // 允许某个文件不含版本号信息 
            // 参考: https://github.com/rinh/fekit/issues/11
            {
                "path" : "./scripts/page-c.js" , 
                "no_version" : true
            }
            
        ] ,

        // 自动化hook脚本, 请参考 https://github.com/rinh/fekit/issues/10 , https://github.com/rinh/fekit/issues/12
        "scripts" : {
            "premin" : "./build/premin.js" ,
            "postmin" : "./build/premin.js" ,
            "prepack" : "./build/premin.js" ,
            "postpack" : "./build/premin.js"
        }
    }

## 为fekit贡献代码

fekit是一个插件化, 易于扩展的工具集, 如果你愿意为它增加功能, 请看下面的内容

fekit所有源码全部使用coffeescript开发

* bin - 放置可执行文件
* lib - 执行代码(编译结果,请不要修改)
* src - 源码
* test - 单元测试
* testcase - 测试用例, 模拟了一个真实项目
* Cakefile - 部署文件

#### 如何增加一个命令  ####

请在`src/commands`增加文件 {命令名}.coffee

一个命令请包含如下内容

    # 命令的使用说明
    exports.usage = "使用说明"
    
    # 命令的参数定义, 请参考optimist的使用方法
    exports.set_options = (optimist) ->
    
    # 命令入口
    exports.run = (options) ->

#### 如何增加一个编译处理类型 ####

请在`src/compiler/plugins`增加文件 {编译处理后缀名}.coffee

其中必须存在的方法是

    # 决定该编译方式是用于哪类处理
    exports.contentType = "javascript 或 css"
    
    # 处理方法
    # @source 待处理文件的内容
    # @path 待处理文件的路径
    # @callback( err , result ) 处理完成的回调
    # 返回结果应该编译结果 
    exports.process = ( source , path , callback ) ->

