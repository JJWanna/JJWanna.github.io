---
title: KOA实战
date: 2017-08-16
tags: Redux
---

### 前言：
- yarn官网：https://yarnpkg.com/
- 安装yarn: npm install yarn -g


##＃ 一：先搭整体页面结构
#### 1、搭建整体结构

备注：【简述各个目录下的作用】
1. conf 是放置相关配置；
2. controllers放置路由模块；
3. midware 是放置中间件；
4. node_modules是放置下载的模块；
5. public下是放置静态资源；
6. views是放置模板；
7. models放置与数据打交道的模块；
8. 添加启动文件 app.js；

<!--more-->

#### 2、创建配置文件 yarn init
（1）执行命令如下所示：
![image](http://note.youdao.com/favicon.ico)

（2）页面结构如下所示:

（3）这是创建的package.json文件里的内容：

#### 备注：
npm init 创建的配置文件与 yarn init 创建的配置文件唯一的差别就是 npm init创建的配置文件 里多了个scripts；
scripts用来集成自定义命令；
所以在这里，为yarn init创建的配置文件添加 scripts一项；如下图所示：

#### 3、安装koa2，命令：yarn add koa@next，如下图所示：
（1）执行命令：

（2）下载完后，可以看到在根目录里有这个yarn.lock文件，如下所示：

#### 备注：yarn.lock就是保存了下载的插件的所有信息；当我们删除了模块时，再去下载，那么yarn就会根据这个文件进行下载，这时速度会快很多；因为有缓存；
#### 4、编写conf目录下的config.js,配置端口，如下图所示：

#### 备注：将配置的信息，导出去；
#### 5、编写app.js，如下图所示：

#### 6、此时启动app.js，报错；是因为node不支持es6,所以此时要下载babel去转码，如下图所示：


### 二：配置babel
#### 1、创建并配置.babelrc文件，如下图所示：

#### 2、通过yarn下载 babel-preset-es2015 和 babel-preset-stage-3，命令如下所示：
- yarn add babel-preset-es2015
- yarn add babel-preset-stage
- 安装 babel-cli, 如下图所示：

#### 5、windows下局部安装babel模块以后，无法直接使用babel命令，如何解决？
==备注：创建babel.cmd，并将node .\node_modules\babel-cli\bin\babel.js %*粘贴到该文件里去。==
#### 6、可以在package.json里配置文件，如下图所示：

#### 7、输入npm run build,编译成功，如下图所示：
（1）输入命令：

（2）编译后的效果：

备注：此时说明babel可以进行编译文件了；
#### 三：关于路由这块的处理：
#### 1、下载第三方路由插件 koa-simple-router, 如下图所示：

#### 2、在controller目录里开始编写路由模块，如下所示：
1. 这是目录结构：
1. 备注：创建了两个文件，第一个文件是controllerinit.js；第二个文件是indexcontroller.js;
1. 编写文件controllerinit.js里的内容，如下图所示：
1. 编写文件indexcontroller.js里的内容，如下图所示：

#### 四：下载插件 koa-swig 和 co模块，如下图所示：
1、下载koa-swig, koa-swig的作用是渲染模板, 下载过程如下所示：

2、下载co模块 , co的作用是将generator包一层，替它执行next()，下载过程如下所示:

3、在config文件里配置模板路径，如下所示：

4、在app.js里面koa-swig的模板渲染配置参数，如下所示：

#### 五：接着下载第三方插件 koa-static,  指定静态资源路径，配置渲染静态资源，如下图所示：
##### 1、下载第三方模块 koa-static,如下所示：

##### 2、在配置文件config.js里 配置 静态资源的路径，如下所示：

##### 3、在启动文件里指明 静态资源所在位置,如下所示：

##### 4、第3步，用到了convert，koa-convert是koa1转换器，可以将koa1转换为koa2;
###### （1） 安装 koa-convert 第三方插件，如下所示：

###### （2）在启动文件里引入 koa-convert,如下所示：

#### 六：处理模板
#### 1、在views里创建了两个模板index.html、layout.html;
#### 2、layout.html为基本模板，如下图所示：

备注：node-swig的api地址：http://www.cnblogs.com/elementstorm/p/3142644.html
### 3、index.html去继承layout.html，如下图所示：

#### 4、将配置文件下的config.js改为config.es; 将启动文件app.es改为app.js，如下图所示：

备注：因为node不支持es6的某些语法；所以需要编译；然后用编译后的文件；所以这里把源文件都用es格式；
#### 5、在package.json里配置多任务，如下图所示：

备注：配置多个任务，就如同本例所示，babel里配置了多个任务，npm run 命令1 && npm run 命令2
#### 6、执行npm run babel命令以后，成功编译文件，如下图所示：
（1）执行命令：

（2）页面结构图：

#### 7、启动编译后的文件，此时服务已启动，如下图所示：

#### 8、客户端去访问服务端，如下图所示：

备注：此时说明已访问成功
#### 9、此时来渲染模板，和静态资源，那么就需要把编写路由的模块，进行编译：
（1）配置文件里配置编译路由模块的任务，如下图所示：

（2）将路由controllers目录下的文件改为es格式，如下图所示：

（3）此时我们需要在启动文件里去调用编写路由模块的方法所示：

（4）执行 npm run babel ,进行编译，出现错误，如下图所示：

备注：
对于本步骤出现的错误，解决方案是：
第一步：下载 babel-polyfill 的方法： yarn add babel-polyfill
第二步：在启动文件里引入 这个编译插件，如下所示：

备注：babel-polyfill主要解决编写的路由模块进行编译时的报错；
第三步：此时再去编译文件， npm run babel，就不会再报错；
（5）启动app.js文件，启动服务，如下图所示：

（6）客户端进行访问，如下图所示：

备注: 此时说明，后端的路由已配置成功，后端可以成功渲染模板；
七：将页面放置模板内，将js和css资源放置public下
1、public的scripts目录下放置js, stylesheet目录下放置css,如下图所示：

备注：
- （1）scripts下的jquery.min.js是jquery库，用来把导出来的方法挂载到jquery插件下；
- （2）scripts下的system.js 是万能模块加载器，可以将模块加载进来，将方法导出来；
- （3）scripts下的main.js是我们编写自己的业务逻辑；
- （4）stylesheet下的animate.css和index.css 是页面响应的样式；
#### 2、将第一步所示的要编译的文件main.js改为main.es格式，如下图所示：
#### 3、模板里引入这些文件，如下图所示：


#### 4、在配置文件package.json里配置相关任务，关于scripts下的main.es的编译，如下图所示：

##### 5、输入编译命令 npm run babel 进行编译，如下图所示：

#### 6、启动app.js，启动服务，客户端进行访问，如下图所示：
（1）启动服务：

（2）客户端访问：

#### 八：编写路由接收数据，将数据存储数据库操作
#### 1、在controllerinit.es文件里添加数据交互的路由，在indexcontroller.es文件里添加该路由的相关处理逻辑方法，如下图所示：
##### (1)在controllerinit.es文件里添加数据交互的路由

##### (2)在indexcontroller.es文件里添加该路由的相关处理逻辑方法

#### 2、下载 mysql-promise 插件，命令：yarn add mysql-promise，如下图所示：

#### 3、启动数据库，建表，如下图所示：

备注：
（1）我在这里用了warmpserver里的phpadmin来建数据库；
（2）我建了数据库 db_yideng; 表zan; 表有两个字段，一个字段为id，int类型，主键; 一个字段为num,int类型；
#### 4、在 model目录里 创建indexModel.es 文件，处理数据逻辑，如下图所示：





备注：
- （1）model目录里是专门来处理数据的；controllers目录里就是专门来处理路由的；二者不要混淆一起，各自做各自的事情；
- （2）如本例所示：引入数据库模块 mysql-promise
- （3）如本例所示：连接数据库
- （4）如本例所示，编写模块indexModel, 在该模块里添加方法: data()，data里编写操作数据库部分；这里理下思路：
         第一：koa2支持async,所以async是同步的；所以在koa2里面不能出现任何异步东西，需要将异步的统统转化为同步；
         第二：async里的await 后面跟promise时，它会自动取回调函数里的值；这就相当于把异步变为同步；
         第三：在本例当中，我们计算值的时候，用到了定时器，而定时器也是异步，在第一节课的试卷分析里第二道题已详细讲述过；所以这里要把定时器写在promise里，通过await变为同步；
         第四：在本例当中，我用了async的自执行，并且return出去，这样，外面接到的这个async自执行实际上就是个promise,所以我们需要在路由模块 indexcontroller里引入此模块的方法时，需要用async包一层，然后用await来执行这个自执行的asnyc,这样我们才能拿到值；
（5）操作数据库：
  第一：先去查询表，看表里有没有数据；
  第二：若表里没有数据，那么我们此时要向表里插入一条数据；
  第三：若表里有数据，那么此时我们要去更新表里的数据；
  第四：用mysql-promise操作数据库时，如本例所示，回调函数里，我用了affectedRows来判断，API里没有详细说明，需要我们自己去打印找出来这个字段来供我们作为判断的依据；
5、在配置文件package.json里添加编译任务，如下图所示：

#### 6、启动 npm run babel，进行编译文件，如下图所示：

#### 7、启动服务app.js，客户端进行访问，如下图所示：
（1）启动服务，如下图所示：

（2）客户端进行访问，如下图所示：

（3）查看数据库的变化，如下图所示：

备注：
第一：此时说明我们已做完后端的接口；
第二：本步骤中的第一步打印的值中的截图部分就是第四条最后的部分提示到的，判断数据库到底有没有数据的依据；
九：编写前端的数据交互
1、做事件稀释，防止不停地点赞，前一次还没操作完，后一次又触发了，如下所示：

备注：
在main.es里做事件稀释；
本例当中定时器虽然设置为0，但实际上也是由一定的时间的；
在这个一定的时间内，如果点了n次，那么前n-1次被取消了；只有第n次才执行；
2、编写数据交互，使用axios库，如下所示：
（1）在views下的index.html模板里引入此资源：

（2）在public下的scripts目录下的main.es里添加向后端提交数据，如下所示：

（3）由于点赞的数目由后端计算，所以当后端操作数据库成功时，应该给前端返回该数据，如下图所示：

3、此时编译文件，启动服务，客户端访问，如下所示：
（1）编译文件 npm run babel，如下所示：

备注：太长，我只截取了一部分；
（2）启动服务 node app.js，如下所示：

（3）客户端访问，如下所示：

（4）点击按钮，查看后端数据库是否变化？如下所示：
第一步：点击按钮，提交数据，如下所示：

第二步：查看数据库里的数据变化，如下所示：

备注：
此时所有流程走通；接下来该做测试；
十：测试
1、前端测试【UI测试】：
（1）安装
第一步：全局安装karma,这样可以让karma在任何地方运行：npm install -g karma-cli
第二步 本地安装karma：yarn add karma
备注：本地安装karma
第三步 安装断言库和chrome启动器：yarn add karma-jasmine karma-chrome-launcher
备注： karma-jasmine是个断言库； karma-chrome-launcher是个chrome的启动器；
第四步：安装 jasmine-core 库：yarn add jasmine-core
备注：jasmine-core      断言库核心
第五步：安装 karma-phantomjs-launcher 和 phantomjs ：yarn add karma-phantomjs-launcher phantomjs
备注：
karma-phantomjs-launcher：   phantomjs的启动器
phantomjs：                              无界面浏览器，但是它包含了所有浏览器的功能；
如果要使用phantomjs，那么就没必要再去下载 chrome的启动器了；我们使用这个便可；
删除某个库  yarn remove;
在这里，我本打算下载phantomjs，下载中发生错误，下载不下来，所以我还是用chrome的启动器；
（2）配置
第一步：输入karma init，进行配置，如下所示，去选择相应的：


第二步：此时生成 karma.conf.js,如下所示：

第三步：我去修改配置文件 karma.conf.js,如下所示：


备注：图中所圈的部分
basePath：表示 基本的路径配置；
frameworks：表示测试所依赖的库；
files：表示测试哪些文件；
exclude：表示排除哪些文件不测试；
browsers：表示karma选择哪个浏览器，由于我无法下载phantomjs,所以我这里用了chrome;
singleRun: 设置为true, 默认是false; true表示，如果我们用的是Chrome启动器，如果没有报错，那么当我们karma start时，自动打开谷歌浏览器，然后关闭；
第三步：创建目录 test , 去测试相应的文件，如下所示：
test目录下的文件，如下所示：

public目录下的scripts下的index.js文件如下所示：

（3）开始进行测试
输入karma start  , 测试如下所示：

备注：这种情况表示无错误；若有错误提示，那就是我们写的东西有问题；
（4）public目录下的scripts下的index.js文件, 我主要用在这里：如下所示：

2、后端单测(端对端)测试【unit测试】：
(1)安装 yarn add selenium-webdriver  如下图所示：


备注：
此时说明node版本过于低下，需要下载大于6.9.0版本的node
更新node版本以后，继续下载，下载成功。

（2）在test目录里创建e2e.js文件，如下所示：

（3）进入test目录，输入node e2e.js 进行测试，如下所示：

备注：
在第二步的forBrowser里设置的是firefox, 那么此处提示错误就该图所示，要去图中所示的网址去下载启动firefox的插件；
若在第二步的forBrowser里设置的是firefox, 那么此处提示要下载的网址就是：http://chromedriver.storage.googleapis.com/index.html
我这里选择了chrome;
（4）根据提示，去该网站上下载东西，如下所示：

备注：
我选择的最新的版本 2.9，点击进去以后，选择图中所标志的选择 chromedriver_win32.zip 进行下载， 解压以后，将此文件放在 test目录下，与e2e平级，如下所示：

（5）启动 app.js，启动服务，如下所示：

（6）进入test目录，启动测试文件，如下所示：

备注：
如果没有出错，那么完成测试以后，自动退出；
当我们启动node e2e.js时，它会自动去打开谷歌浏览器，模仿人的一系列操作；如果结果匹配成功，那么会自动关闭浏览器，回到这里来；然后退出e2e.js的测试；
3、后端 服务测试：
（1）全局安装mocha, 如下所示：

（2）安装第三方模块 supertest, 如下所示：

（3）在test目录下创建 service.js 文件，该文件里的内容，如下所示：

（4）用mocha 进行测试，如下所示：

备注：此时已完成。
4、在配置文件里，编写UI测试 、服务端测试、unit测试命令，并将三个命令合并在一起，进行测试，如下所示；

备注：
（1）只有 UI测试 和 服务端测试命令可以合并在一起，同时测试；
        因为服务端测试时，不能启动app.js否则会报错；而unit测试，需要启动app.js，否则会报错；
（2）当我们在编写unit测试命令时，我们需要把test目录下的此文件放在与配置文件同级目录，否则执行unit测试的时候，会报错，提示我们去下载该插件；