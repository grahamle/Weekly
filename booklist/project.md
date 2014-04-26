# 项目文件说明文档

**Q**：目前来看，在bash中进入client文件夹，命令行中跑`grunt`命令就可以在chrome中跑起来整个app了。那么到底这是个怎么样的流程呢？  
**A**：其实`grunt`之后，grunt这个构建工具就会在client文件夹下去找`Gruntfile.js`（G一定要大写），然后通过grunt的配置文件去执行任务，这就是整个**项目构建的入口**（和项目app入口不同）。下面先来看下整个项目的目录组织结构，然后再大致细说。

## 目录

- [项目目录组织结构](#p1)
	- [/client](#p10)
	- [/server](#p11)
- [client外围文件](#p2)
	- [Gruntfile.js](#p20)
	- [package.json](#p21)
	- [devsettings.json](#p22)
	- [books.json](#p23)
	- [bower.json](#p24)
	- [.jshintrc](#p25)
	- [.editorconfig](#p26)
	- [.bowerrc](#p27)
	- [karma](#p28)
- [以状态切换的角度看项目](#p3)
    - [index.html](#p30)
    - [app.js](#p31)
    - [home](#p32)
    - [home.shelf](#p33)
    - [home.shelf.index](#p34)
    - [home.reader](#p35)
    - [home.reader.index](#p36)
    - [home.reader.card](#p37)
    - [home.reader.special](#p38)
- [client/app/scripts/controllers](#p4)
	- [shelf.js](#p40)
	- [reader.js](#p41)
	- [readerIndex.js](#p42)
- [client/app/scripts/directives](#p5)
	- [etAttrs.js](#p500)
	- [mainSwipe.js](#p501)
	- [card.js](#p502)
- [client/app/scripts/filters](#p6)
- [client/app/scripts/services](#p7)
	- [user.js](#p70)
	- [audio.js](#p71)
- [client/app/views/reader](#p8)
	- [reader.html](#p80)
	- [readerIndex.html](#p81)
- [client/app/views/shelf](#p9)
	- [shelf.html](#p90)
	- [shelfMain.html](#p91)
	- [shelfSide.html](#p92)
- [client/app/Documents](#pA)
	- [package.json](#pA0)

## <a name="p1">项目目录组织结构</a>

### <a name="p10">client文件夹</a>

	client                                  	-- 所有客户端应用的代码
		.tmp                          			-- 临时文件夹
			scripts								-- 临时js
		app 									-- 所有的业务逻辑存放
			Documents							-- 本地缓存电子书资源（HTML文件）
				6f6cfc57-...					-- 高中英语第一册教材HTML文件
				72a5bc15-...					-- 高中英语第二册教材HTML文件
				...								-- 其他教材的HTML文件
			components 							-- 类库依赖
				angular 						-- AngularJS框架
				...								-- 其他前端依赖
			index.html 							-- 整个应用的入口点
			script 								-- 所有业务逻辑的实现代码
				app.js 							-- 整个应用的页面路由选择及模块继承（js的入口点）
				controllers 					-- 控制器代码文件夹，控制业务逻辑
				directives 						-- 指令文件夹，用于扩展HTML模版
				filters 						-- 过滤器，提供数据格式化等功能					
				services 						-- 公共服务及公用业务逻辑代码					
			styles 								-- 整个应用的样式控制
				font 							-- 应用中用到的字体资源
				img 							-- 应用中用到的全局图片资源
				app.styl 						-- 整个应用的所有样式代码
				book.styl 						-- 控制书本的样式代码
				common.styl 					-- 公用的样式
				main.css 						-- 通过stylus将多个.styl文件整合编译后生成的
				main.styl 						-- 对所有.styl文件进行整合
				ui.styl 						-- 控制一些框架上的ui样式
				variables.styl   				-- 存放stylus变量
			views 								-- 应用所用到的所有模版
				reader 							-- 书本内各界面模版
				shelf                       	-- 书架界面模版			
		
		node_modules							-- npm安装的开发环境所需要的依赖包
			
		books.json 								-- 所有教材信息
		bower.json 								-- 阐明应用的各依赖信息
		devsetting.json 						-- 目前为DB服务的信息
		package.json 							-- 项目元数据，grunt跑起来时加载各依赖包的列表信息

		Gruntfile.js 							-- 自动化工具Grunt的配置文件
		karma-e2e.conf.js 						-- karma端到端测试
		karma.conf.js 							-- 

### <a name="p11">server文件夹</a>

	server 										-- 目前未有内容
		.gitignore								--

### README文件夹

	README 										-- 项目相关的规范
		.editorconfig 							-- 编辑器配置规范
		README.md 								-- 项目开发指南（包括开发环境配置、git配置等）
		items.md 								-- 教材编写指南

### 其他顶层项目文件

	.gitignore 									-- 无他
	Gruntfile.js 								-- Grunt 的配置文件
	package.json 								-- 项目元数据
	README.md 									-- README 入口


## <a name="p2">client外围文件</a>

### <a name="p20">Gruntfile.js</a>

Grunt 是一个任务自动化工具（task runner），Gruntfile.js 为 Grunt 的配置文件。关于 Grunt 的用法和配置等，可以参考 [Grunt 主页](http://gruntjs.com/)。

**Q**：代码前面16行都是干嘛的？  
**A**：这里面主要声明一些下面配置的时候要用到的变量和信息。详述下面几个变量：

- lrSnippet ：livereload 脚本片段，因为 livereload 需要通过在 html 中嵌入一段代码来引用入口文件，此文件在最终发布时是不需要的，故通过一个 connect 中间件仅在开发调试中实时嵌入。
- uuid ：通过依赖 node-uuid 模块来生成唯一的随机 id
- mountFolder ：用于方便配置 connect 静态文件中间件的函数，方便的 mount 一个目录供 connect 读取。
- modRewrite ：加载 connect-modrewrite ，一个 connect 中间件，可以给 connect/express 的 server 提供 URL 重写功能。用于适配 Angular 的 HTML5 Mode，将所有请求重定向给 index.html
- swig ：模板引擎，用于预处理一些用模版生成的教材内容，如单词表等。

**Q**：500行的 js 代码，到底都干了些啥？  
**A**：别看代码行数挺多，其实逻辑倍儿清晰。总体就干了四件事：

- 通过 forEach() 加载项目依赖的所有 grunt 插件
- yeoman 配置及异常检测
- grunt.initConfig() 是配置主体，里面是一个 js 对象，定义所有 grunt 插件的配置（共达345行）
- grunt.registerTask() 注册 grunt 任务。

**Q**：命令行中执行的一个 grunt 命令代表什么？  
**A**：grunt 不带参数运行会执行 Gruntfile.js 中的默认任务，目前的默认任务为 `server` 任务。

**Q**：这个 js 文件最后是怎么被用的？  
**A**：整个 js 文件是通过 `module.exports()` 作为 node.js 模块暴露在外的，grunt会在当前目录下通过文件名找到这个文件，据此进行任务执行。

**Q**：那么 `grunt.initConfig()` 里到底有哪些任务呢？  
**A**：`initConfig` 是各个 grunt 插件任务的初始化配置（**注**，带 grunt-contrib- 前缀的，是 Grunt 的官方插件），具体如下：

* `yeoman`：配置自定义变量 yeomanConfig。
* `livereload`：配置 [grunt-contrib-livereload](https://github.com/gruntjs/grunt-contrib-livereload) 插件，用于检测文件变化并自动刷新浏览器。此插件已经被废弃，后面会升级为 [grunt-contrib-watch](https://github.com/gruntjs/grunt-contrib-watch)。
* `watch`：配置 [grunt-regarde](https://github.com/yeoman/grunt-regarde) 插件，用于监视文件变化并执行其他任务，目前做了 alias 为 watch。此插件已经被废弃，后面会升级为 [grunt-contrib-watch](https://github.com/gruntjs/grunt-contrib-watch)。
* `replace`：配置 [grunt-text-replace](https://github.com/yoniholmes/grunt-text-replace) 插件，用来替换文本，包括几个 target 配置，分别替换代码中的不同部分。
* `connect`：配置 [grunt-contrib-connect](https://github.com/gruntjs/grunt-contrib-connect) 插件，用于开启一个 [connect](http://www.senchalabs.org/connect/) web 服务器供开发时使用。
* `open`：配置 [grunt-open](https://github.com/jsoverson/grunt-open) 插件，打开特定文件，这里打开的是 url，故会调用系统默认浏览器。
* `clean`：配置 [grunt-contrib-clean](https://github.com/gruntjs/grunt-contrib-clean) 插件，用于清理临时文件。
* `jshint`：配置 [grunt-contrib-jshint](https://github.com/gruntjs/grunt-contrib-jshint) 插件，用于自动 hint js 文件，**暂未使用**。
* `karma`：配置 [grunt-karma](https://github.com/karma-runner/grunt-karma) 插件，用于自动化测试，目前**暂未使用**。
* `concat`：配置 [grunt-contrib-concat](https://github.com/gruntjs/grunt-contrib-concat) 插件，做文件的合并。
* `usemin`：配置 [grunt-usemin](https://github.com/yeoman/grunt-usemin) 插件，用于将 HTML 中对一系列文件的引用合并为一个引用。
* `useminPrepare`：同上，是 usemin 的一部分。
* `imagemin`：配置[grunt-contrib-imagemin](https://github.com/gruntjs/grunt-contrib-imagemin) 插件，用于优化图片文件，**暂时未用**，项目中尚未用到图片。
* `cssmin`：配置 [grunt-contrib-cssmin](https://github.com/gruntjs/grunt-contrib-cssmin) 插件，用于 css 文件压缩。
* `ngmin`：配置 [grunt-ngmin](https://github.com/btford/grunt-ngmin) 插件，用来压缩 Angular 相关的 js 代码，因 Angular 使用了 [DI](http://docs.angularjs.org/guide/di)，代码较为特殊，ngmin 可解决此问题。
* `uglify`：配置 [grunt-contrib-uglify](https://github.com/gruntjs/grunt-contrib-uglify) 插件，用于代码混淆。
* `rev`：配置 [grunt-rev](https://github.com/cbas/grunt-rev) 插件，用于给静态文件加版本号以解决缓存问题。
* `copy`：配置 [grunt-contrib-copy](https://github.com/gruntjs/grunt-contrib-copy) 插件，用于复制文件到指定目录，目前用于将文件复制到构建目录。目前里面的几个 ios 等 target 暂未用到。
* `stylus`：配置 [grunt-contrib-stylus](https://github.com/gruntjs/grunt-contrib-stylus) 插件，用于预处理 styl 文件为 css 文件。
* `shell`：配置 [grunt-shell](https://github.com/sindresorhus/grunt-shell) 插件，用于执行 shell 命令，目前用到的是通过 git 抓取和更新教材内容。

**Q**：好人，最后告诉我一下这些任务是怎么被执行的吧？要按顺序么？还是乱序随便？  
**A**：grunt 的任务是在 Gruntfile.js 中通过 registerTask 指定，之后在命令行通过参数读入任务名并执行的。具体参考 [Grunt 主页](http://gruntjs.com/)。

### <a name="p21">package.json</a>

**Q**：该文件不就是个 json 文件么？很重要么？都放啥呀？  
**A**：此文件标识了项目的元数据信息，npm 会从此文件中存取需要安装的依赖，grunt 也从此文件中读取相关依赖。如果在安装 npm 模块时提供了 `--save` 或 `--save-dev` 参数，则会将对应模块作为依赖写入此文件的对应位置。关于 package.json 的各项设定，可以参考 npm 文档 [package.json(5)](https://npmjs.org/doc/files/package.json.html) 部分。

### <a name="p22">devsettings.json</a>

**Q**：目前这个文件是干嘛的？  
**A**：这个文件现在只存了两个属性，一个是开发时的服务器地址，另一个是发布时用的服务器地址。在开发或构建阶段，会被用来替换文件中的对应变量。

### <a name="p23">books.json</a>

**Q**：books.json 这个文件主要就是存储了所有教材的一个快照信息，包括教材名称，在系统中拥有的唯一的 uuid 以及教材在本地git服务器中的 url 地址，总共包括四个条目，通过放在一个数组作为 `books` 属性的值。而另外一个属性为 `"dist" : "app/Documents/"` ，这个属性频繁使用，但不能精确去讲出它用作什么？而且这个 json 文件是否有可能放到更深一点的目录中去，因为从逻辑关系上讲，外围都是放的跟打包/构建工具相关的文件和代码。  
**A**：此文件存储教材的部分信息，用于在开发阶段对教材做处理时使用，如抓取和更新服务器上的教材，预处理模板等。所以并非是教材的一部分，而是在构建和开发阶段才会用到的文件。`dist` 表明了教材的存储路径。

### <a name="p24">bower.json</a>

**Q**：我知道这个文件中存放的都是开发过程中所要用到的依赖，**而非打包/构建时要用的**，这里 `dependencies` 和 `devDependencies` 具体有什么差别呢？还有个 `resolutions` 属性？  
**A**：Bower 的资源配置文件，具体可以参考 [Bower 主页](http://bower.io/)。`devDependencies` 是在开发阶段用到的库，如，和测试相关的库等。`resolutions` 用于指定在安装未明确版本的组件时，使用哪个版本。。

### <a name="p25">.jshintrc</a>

**Q**：这个文件是不是就是用于 Gruntfile.js 中的 `jshint` 任务下的一个可选参数列表传入，然后来控制是否对哪些模块的 js 代码进行规范还是进行提示？  
**A**：`.jshintrc` 是 jshint 的参数配置文档，具体可参考 [jshint 主页](http://www.jshint.com/)。项目中的 Grunt 任务里有一个 grunt-contrib-jshint 的配置，用来在构建时自动 hint js 代码，但目前并未启用。另一方面，如果编辑器支持 jshint，或安装了 jshint 插件，也会自动读取此配置文档，对 js 文件做 hint 并给出提示和建议，有助于写出更加规范的代码。

### <a name="p26">.editorconfig</a>

**Q**：不晓得为什么这种后缀名就可以是表示编辑器配置？是特定的么？里面的注释书写方式好熟悉。  
**A**：`.editorconfig` 是一种约定规范，支持此规范的编辑器会自动读取项目中的此文件，并按其标准格式化代码。如自动设定缩进符，自动删除行尾空格等，具体可参考其项目主页 [http://editorconfig.org/](http://editorconfig.org/)。

### <a name="p27">.bowerrc</a>

**Q**：貌似这个文件里面放了一个 `directory` 属性，值和 books.json 中的 `dist` 属性一样，但是却没有用到，这个文件有用么？  
**A**：此文件为 bower 的配置文件，`directory` 属性用于指定当 `bower install` 时组件要安装到哪个目录。关于 .bowerrc 和 Bower，具体可参考 [Bower 主页](http://bower.io/)

### <a name="p28">karma-e2e.conf.js | karma.conf.js</a>

**Q**：这两个文件我知道是做测试用的，具体怎么嵌入到项目中的？    	
**A**：这两个文件是自动化测试工具 [Karma](http://karma-runner.github.io/) 的配置文件，e2e 是端到端测试的配置，另一个是单元测试的配置。**暂未使用**。


## <a name="p3">以状态切换的角度看项目</a>
通过 [app.js](#p31) 进行页面加载的切换状态的控制后，目前应用跑起来后的第一个页面会是用户个人所有教材的页面，接下来的介绍方式采用的是：**通过介绍一个页面的模版以及模版的各个模块所用到的逻辑功能，去引用的js文件，穿插讲述 scripts 文件夹下的相关文件，属于绑定的套餐式**。

下面画个图应该更能直观理解整个app的状态切换。

![状态切换图](project_state.png)

### <a name="p30">index.html</a>

**Q**：我知道 `index.html` 是项目前端的唯一入口，grunt 通过 `connect` 加载的就是 `index.html` 文件，这个文件在body块中就只有唯一一个可视化标签，一个类为root的div标签，能解释下这个么？  
**A**：我看就直接讲下这个文件的构造好了。

- `head` 标签部分：
	+ 三个 `meta` 标签，定义字符集、不设置网页手机号的拨号连接、以及 `viewport` 控制移动设备屏幕的显示
	+ 一个 `title` 标签，不解释
	+ 六个 `link` 标签，用来载入项目所需要的css样式的，`main.css` 是 stylus 编译生成的，另外五个都是跟字体样式相关的，后面会使用本地字体并集成为一个文件。
- `body` 标签部分：
	+ 一个可视化 `div` 标签，最关键的就是通过 `ui-view`（而不是 `ng-view`）载入模版视图，使用的是 angular-ui 中的 ui-router 模块，至于载入什么样的模版视图，这个是在 [app.js](#p31) 中进行控制
	+ 其他则是60+个载入 `scripts` 文件夹及 `components` 文件夹下的 js 文件

### <a name="p31">app.js</a>

- 总体逻辑：返回一个 `angular` 的 module ，让变量 `app` 指向这个 module 的引用，整个 module 依赖于另外两个 module ：`hmTouchEvents` 和 `ui.router`

- module 内部逻辑划分：总体上讲一个 `angular module` 包含配置和运行两个阶段，而这个文件中也是很清晰地执行了这个逻辑：
	+ `module.config()` ：module 的配置阶段，有四个依赖注入对象（都来自 `ui.router` 模块么？不是，有些是其他模块。）
		- `$locationProvider`：用来启用html5模式，可以看到url中的参数中的#号会消失
		- `$parseProvider`：angular 1.2 中 promises 在 view 中不会自动展开，这个选项控制让其自动展开（即旧的行为），但会在控制台报警告，这是一个过渡时期的做法，以后会去掉。目前代码中已经将需要展开的部分改过了。
		- `$urlRouterProvider`：控制默认的url导航引导至书架的那个html模版页面下
		- `$stateProvider`：这是整个文件的重头，是让页面可以成为SPA的重型武器，根据url的变化切换浏览器上应该加载哪些模版页面和数据，目前总共又**七个**状态跳转：
			+ [home](#p32)
			+ [home.shelf](#p33)
			+ [home.shelf.index](#p34)
			+ [home.reader](#p35)
			+ [home.reader.index](#p36)
			+ [home.reader.card](#p37)
			+ [home.reader.special](#p38)
	+ `module.value()` ：注册了两个预先初始化的对象，之后在项目别的脚本中都可以通过DI机制进行引用
		- `appConstants`：一个js对象，包括几个有关应用的属性
		- `$anchorScroll`：用 `angular.noop` 对象。angular 自己有跳转后滚动的功能，但我们的跳转较为复杂，故不使用它（用的是 jQuery.scrollTo 插件），用 `noop` 将其覆盖。
	+ `module.controller()` ：在 module 顶层写的唯一一个控制器，命名为 `MainCtrl`，整个 app 的 controller。做一些阻止默认事件启动hammer事件机制的事（**待完善**）
	+ `module.run()` ：module 的执行阶段，通过DI注入5个对象，主要是往 `$rootScope` 下注册属性（**待完善**）

### <a name="p32">home 状态</a>
根据 [app.js](#p31) 中的相关配置可以知道，从 grunt 的 connect 打开的一个端口的url （localhost:9000）都和所有 states 中的url不同，所以会自动根据 `$urlRouterProvider` 选择将url置为 `localhost:9000/shelf` ，这样，根据 [state的三种触发方式](https://github.com/angular-ui/ui-router/wiki#activating-a-state) 中的第三种：根据url进行state的跳转，这样我们就触发了和这个url对应的 `home.shelf` 状态或者 `home.shelf.index` 状态，而又根据 [嵌套状态的触发](https://github.com/angular-ui/ui-router/wiki/Nested-States-%26-Nested-Views#nested-states--views) 我们得知，**只要一个状态是处于激发状态，那么它的所有祖先状态也被触发**，而且将 `abstract` 属性设置为 true 的状态是不能自我触发的，必须是由其子状态牵动触发，这样，我们知道最开始触发的是 `home.shelf.index` 状态，然后激活 `home.shelf` ，最后它又带动 `home` 状态也被触发了。

由上可知，虽然 `home.shelf` 状态先被触发，但是离不开 `home` 状态，最终呈现的视图也是两个状态下的模版的叠加组合，所以一下子我们就先说 `home` 状态。

`home` 状态需要多个文件及文件代码块协作达到，主要有如下：

- 模版块：被嵌到 [index.html](#p30) 的 `ui-view` 所在的 div 中，而且其本身也含有 `ui-view` 指令，将 `home.shelf` 状态对应的模版载到这个 div 中，实现嵌套
- 模板中用到的 `ng-controller` ：是写在 [app.js](#p31) 文件中的唯一一个全局的 controller ：`MainCtrl`
- `state()` 配置中的 resolve ：用于设置需要注入到控制器中的依赖服务，这里是去拿 `userService` 取回的用户信息数据
- `state()` 配置中的 controller ：将 上一步 resolve 得到的 `userDataResolved` 赋值给变量 `userData` 并将该变量注册到 `$rootScope` 下

**注**：关于 ui-router 的路由机制，请对比 angular 原生的 `$routeProvider` 进行学习，效果倍加。  
[angular的路由机制](http://www.codingcool.com/2013/08/02/angularjs%E7%9A%84route/)

### <a name="p33">home.shelf</a>
这个状态是 abstract 状态，不能自我触发，是由其子状态 `home.shelf.index` 触发的，当然，先讲讲这个状态包括的两个文件。

- 模版文件：[views/shelf/shelf.html](#p90)
- 控制器文件：[scripts/controllers/shelf/shelf.js](#p40) 文件中的 `ShelfCtrl` 控制器

### <a name="p34">home.shelf.index</a>
这个状态下，由于是嵌套在 shelf 的最里面，外面的控制逻辑基本上都已经健全，它所做的其实就是填充 shelf 界面的主体区域：展示各册书籍即可。所以只有一个文件：[views/shelf/shelfMain.html](#p91)

### <a name="p35">home.reader</a>
home.reader 状态被设置为 abstract 状态，一个说明不能自我触发，另外一个，它把自己状态中的url属性值预先插入到自己的子状态中了，所以子状态 home.reader.index 中url值为空，说明就是用了其父状态的url值，那么在url跳转到 `/reader/任一本书id值` 的时候，home.reader.index 状态先被触发，然后引起其父状态 home.reader 被触发。进而两个状态协同完成**教材单元间页面**的加载及显示。那按照上面介绍 shelf 界面相关状态的规矩，这里也先介绍 home.reader 状态。

这个状态下 `module().state()` 中主要有如下几个属性：

- url ： '/reader/:bookId' （没什么特别的）
- abstract ：true （上面说过了）
- templateUrl ：[/views/reader/reader.html](#p80) （重点一）
- resolve ：{ ... } --> 该属性用于返回需要注入到控制器中的依赖服务，这里是去拿教材仓库下某一册教材文件下的教材缩略信息 [package.json](#pA0) 文件，以及搜索索引的索引查询表，返回两个依赖服务，名字分别为：`packageInfo` 和 `searchIndex` （尚不明确）
- controller ：'ReaderCtrl' --> 这个状态下是用在 [scripts/controllers/reader/reader.js](#p41) 文件中定义的 `ReaderCtrl` 控制器来进行相关业务逻辑的控制

### <a name="p36">home.reader.index</a>
在该状态下，模版文件被插入到母状态 home.reader 的 `<section>` 标签中，然后以一个控制器进行主体区域的控制。主要有两个文件：

- 模版文件：[/views/reader/readerIndex.html](#p81)
- 控制器文件：[/controllers/reader/readerIndex.js](#p42)

### <a name="p37">home.reader.card</a>
若点击了上面 readerIndex.html 模版的某个单元，该状态被触发，进而加载自己相关的模版文件。而这样的加载有一个选择逻辑：

- 如果用户是通过点击单元进来到这个状态，那显然用户只在url上多加了一个 unitId ，没有加 cardId ，所以这时候就自动导向默认的单元主页，将 cardId 置为0，加载 Documents 文件夹下的 card0.html
- 如果用户是在学习过程中点击某个card，那显然 `cardId > 0` ，这样，就加载相关的card的html页

### <a name="p38">home.reader.special</a>
在上面的状态中都不是用户想要加载的页面时，跳转这个 special card 状态，special 这里指的就是用来加载词汇表的相关页面。词汇表的处理又分为三个层级，略微复杂，以后详述。


## <a name="p4">client/app/scripts/controllers</a>
接下来这个部分的都是要具体介绍代码的逻辑，就不用Q&A的形式了，采用列表形式，尽量简洁。controllers文件夹内部分为 reader 和 shelf 两层，但是为了方便，我们就直接不把分层在这边展示，也可以很清晰。

### <a name="p40">shelf.js</a>
该文件定义了 `ShelfCtrl` 控制器，在 [home.shelf](#p33) 状态中使用，主要就是往 `$scope` 中写入一个 `books` 属性，它是一个具有四个条目的数组，代表四册教材，每个条目是一个对象，有 `name` 和 `bookId` （随机产生）属性。这个 `$scope` 里的东西，就可以通过 scope hierarchy 被 `home.shelf.index` 继承并使用。

### <a name="p41">reader.js</a>
该文件定义了 `ReaderCtrl` 控制器，在 [home.reader](#p35) 状态中使用。共有两三百行代码，代码较复杂，说明往 $scope 中注册的属性非常多，而且后代 $scope 也正是在继承了那么多的属性下才有强大的功能。

#### 依赖服务

- $scope ：当前视图下域下生成的 Scope 对象
- searchIndex ：`home.reader` 状态下 resolve 返回的依赖对象（搜索用的索引表），见 [app.js](#p31)
- packageInfo ：`home.reader` 状态下 resolve 返回的依赖对象（对于当前教材的缩略信息），见 [app.js](#p31)
- userService ：在模块下通过 factory 创建的，见文件 [services/user.js](#70)
- $timeout ：未知
- audioService ：在模块下通过 factory 创建的，见文件 [services/audio.js](#71)
- $filter ：未知

### <a name="p42">readerIndex.js</a>
目前只在这个控制器中往 $scope 中注册了一个 `continue` 函数，未充分实现，目前的实现也只是调用 `$scope.gotoCard()` 这一从母scope中继承而来的方法进行一个**假跳转**。

## <a name="p5">client/app/scripts/directives</a>
这个文件夹目前比较杂，个人觉得可以分为两类，一类是具体题型的指令，另一类是用于扩展标准html标签的属性的指令。**以后会按照这个思路进行划分**。

### <a name="p500">etAttrs.js</a>
这个指令集对html标准标签的属性进行扩展，目前总共有5个：

- `etHref` ：在html文件中，使用时用 `et-href` 的形式，扩展 `href` 属性
- `etTrans` ：尚不明确
- `etSrc` ：扩展 `src` 属性
- `etActivable` ：尚不明确
- `etTransitionend` ：尚不明确

### <a name="p501">mainSwipe.js</a>
这个文件首次出现在 [reader.html](#p80) 中的，主要是控制左滑右滑的逻辑。

### <a name="p502">card.js</a>
这个文件定义的 card 标签，首次出现在 [reader.html](#p80) 中。

### <a name="p503">homeworkCard.js</a>
这是我这两天要急需完成的。


## <a name="p6">client/app/scripts/filters</a>
...


## <a name="p7">client/app/scripts/services</a>

### <a name="p70">user.js</a>


### <a name="p71">audio.js</a>


## <a name="p8">client/app/views/reader</a>

### <a name="p80">reader.html</a>
这个文件是用在 [home.reader](#p35) 状态中使用，我们知道这个状态的母状态（父状态亦可，突然发现叫母体会好听很多）是 home 状态，所以，它会把 reader.html 这个文件插入到 home 状态中的模版的带有 ui-view 指令的 div 中，同时也就在 $scope 上进行了继承，这边也可以取到由 `userService.getUserData()` 返回的用户数据（其实这个数据是被注册到了 $rootScope 下）。加载之后的html片段看起来应该似如下：

	// template from index.html
	<div class="root" ui-view>
		// template from home state
		<div ui-view ng-controller="MainCtrl" class="mainContainer ng-scope reader">
			// template from reader.html
			<nav class="nav" ng-click="hideSidebar()">
				// 界面侧边工具栏，包括：返回书包、首页、笔记、搜索、设置（首页按钮在此界面下不出现）
			</nav>
			<aside class="cards module hidden" ng-controller="ReaderCardsCtrl">
				// 显示各个cards和作业card的二级导航菜单，在此界面下通过ng-class置为hidden不需显示
			</aside>
			<section ui-view class="main ng-scope index" main-swipe ng-class="mainClass()">
				// 这里就是用于插入 home.reader.index 状态下的模版文件 readerIndex.html
			</section>
			<div class="loadingIndicator ng-scope"> // 加载提示... </div>
			<div class="nextCard ng-scope hidden" ng-class="控制何时hidden的逻辑">
				// 控制阅读到当前card的尾部时可以下拉跳到下个card，但是当前界面隐藏了这个div
			</div>
			<aside class="notificationCenter" ng-class="控制是否打开的状态" ng-controller="NotificationCtrl">
				// 通知中心的面板UI块
			</aside>
			<div ng-class="通知中心按钮状态" count="" notification-toggle>
				// 启用通知中心的那个小铃铛的UI块
			</div>
			<div et-transitionend="" ng-class="">
				// 通过 ng-include 加载搜索、设置、笔记任一面板
			</div>
		</div>
	</div>

接下来讲讲每个UI小物件的实现。

	- <nav> ：左侧工具栏
	  + 返回书包/首页按钮：
		- <li> ：无属性
		  - <a> 
		    et-activable ：让该按钮在被点击和鼠标离开时变换 css 样式
		    ng-show / ng-hide ：通过取 $states.includes('home.reader.index') 的值赋值控制显示哪个逻辑
		    class ：显示哪个逻辑就赋予哪个逻辑的样式
		    ng-href ：通过 ng-href 改写 url ，实现跳转到书架，控制返回书包
		    ng-click ：调用控制器中定义的注册在当前 $scope 下的 gotoIndex() 方法返回首页
		    - <i> ：class属性引入显示的图标
		    - <span> ：文本节点
	  + 二级目录启用按钮：
		- <li> 
		  ng-class ：通过 $scope.state 对象下的 isCardsClosed 属性决定是否加上 current 类，控制显示样式
		  ng-hide ：通过取 $states.includes('home.reader.index') 的值赋值控制是否显示该按钮
		  - <a> 
		    et-activable ：同上
		    ng-click ：调用 toggleCards() 方法控制打开/关闭二级导航菜单
		    - <i> ：同上
		    - <span> ：同上
	  + 笔记/讨论按钮：
	    - <li> 
	      ng-class ：通过 $scope.state 对象下的 currentSidebar  属性决定是否加上 current 类
	      - <a> 
	        et-activable ：同上
	        ng-click ：调用 loadSidebar('notes') 方法，同时通过 $event.stopPropagation() 阻止事件冒泡
	        - <i> ：同上
	        - <span> ：同上
	  + 搜索按钮：同上
	  + 设置按钮：同上
	
	- <aside> ：二级目录
	  class ：控制显示样式的类
	  ng-class ：通过 $scope.state 对象下的 isCardsClosed 属性决定是否添加 hidden 类
	  ng-controller ：用 ReaderCardsCtrl 控制器作用于这个 aside 区域
	  - <div>	
	    class ：homeworks
	    - <homework-card>
	  - <div>
	    class ：cardList
	    - <card>
	      ng-repeat ：通过从 packageInfo.units[].cards 对象中遍历取出每个 card 
	      card ：置为 card ，考虑是有 homework card 的值
	      tap-action ：调用 cardTap(options) 方法   

	- 中间主体区域：
	  - <section>
	    ui-view ：用于加载子状态中的模版页面
	    main-swipe ：用于控制左滑右滑操作的逻辑的自定义扩展属性
	    ng-class ：调用 mainClass() 方法生成相应的类赋值

	- 余下省略

以上相关新出现的指令：

- `et-activable` 和 `et-transitioned` 详见 [directives/etAttrs.js](#p500) 文件
- 调用到的方法见 [controllers/reader/reader.js](#p41) 文件中所定义的 `ReaderCtrl` 
- `main-swipe` 指令详见 [/directives/reader/mainSwipe.js](#p501)
- card 和 homeworkCard 标签详见 [directives/reader/card.js](#p502)

最后，相关方法的调用如果每个文档都写的那么详尽，既没必要，当然，关键是我可能就已经先跪了，所以，**接下来文档只写上新出现的逻辑和比较难弄懂的逻辑**，其余一概不详述。

### <a name="p81">readerIndex.html</a>
这个页面模版的内容主要是单元间界面，总体上分左右两边：

- 左边为继续学习及相关信息：继续学习按钮其实就是调用了 [readerIndex.js](#p42) 中定义的 `continue()` 方法跳转到一个**假的**上次学习界面
- 8个单元的可点击导航：在代表每个单元的 `<li>` 标签上，通过 `ng-click` 调用 `gotoUnit(index)` 这样让应用app又进行了一次跳转，这次跳转将会在之前的url基础上加上单元的参数，现在url像如下所示：  
`http://localhost:9000/reader/72a5bc15-c1b0-4015-b610-df1bc005f6ff/4/0`  
看最后两个参数，不仅仅加上了单元参数，默认还加上了处于哪个 card 的参数。这样子，**根据url匹配激活 state 的原则**，home.reader.card 状态将被激活。


## <a name="p9">client/app/views/shelf</a>

### <a name="p90">shelf.html</a>
该文件是在 [home.shelf](#p33) 中引用，这个文件是要被嵌入到父状态（[home](#p32)）中的含有 `ui-view` 指令的div中。经过从   
`index.html` --> `home中的模版div` --> `shelf.html` 三层的嵌套（均是用 `ui-view` 作为衔接），现在的 div 看起来应该是像下面这样：

	// template from index.html
	<div class="root" ui-view>
		// template from home state
		<div ui-view ng-controller="MainCtrl" class="mainContainer ng-scope shelf">
			// template from shelf.html
			<div class="nav ng-scope">
  				<ul>
  				  <li><a>我的书包</a></li>
  				  <li><a>已购教材</a></li>
  				  <li><a>书城</a></li>
  				  <li><a>设置</a></li>
  				  <li><a>登录</a></li>
  				</ul>
			</div>
			<div ui-view class="main ng-scope">...</div>
		</div>
	</div>

没有什么特别的，有一个需要注意的，就是每个class类中都多了一个 `ng-scope` 类（尚未明确）。而最里层的一个 div 中还有 `ui-view` ，则是用来插入 `home.shelf.index` 状态中的模版的。

### <a name="p91">shelfMain.html</a>
该模版主要填充 shelf 界面的主体区域，在 [home.shelf.index](#p34) 中使用，填充之后从 `index.html` 到目前为止的html结果如下所示：

	// template from index.html
	<div class="root" ui-view>
		// template from home state
		<div ui-view ng-controller="MainCtrl" class="mainContainer ng-scope shelf">
			// template from shelf.html
			<div class="nav ng-scope">
  				<ul>
  				  <li><a>我的书包</a></li>
  				  <li><a>已购教材</a></li>
  				  <li><a>书城</a></li>
  				  <li><a>设置</a></li>
  				  <li><a>登录</a></li>
  				</ul>
			</div>
			<div ui-view class="main ng-scope">
				// template from shelfMain.html
				<div class="ng-scope">
					<ul class="bookList">
						<li ng-repeat="book in books" class="book ng-scope">
							<a et-href="/reader/{ {book.bookId} }">
								<img ng-src="/Documents/{ {book.bookId} }/assets/cover.jpg" />
								<h2 class="ng-binding">{ {book.name} }</h2>
							</a>
						</li>
						// 生成后总共有四个 li ，对应四册教材
					</ul>
				</div>
			</div>
		</div>
	</div>

这里面有两个需要注意的点：

- `ng-repeat` 中用到的 `books` 就是从 `home.shelf` 中的 ShelfCtrl 控制器中的 `$scope` 继承的
- `et-href` 这个自定义指令，是angular中扩展标准html标签属性的指令，定义在 `scripts/directives/etAttrs.js` 文件中，这个文件放在公共文件解释中，[etAttrs.js](#p500)。

### <a name="p92">shelfSide.html</a>
目前尚未使用。


## <a name="pA">client/app/Documents</a>
这个文件夹里面要讲的就主要是几个用到的有关对教材内容进行抽象（抽离）描述的 Json 文件，其他八个单元的各个 card 的内容不在这个文档中讲述。详见 [教材编写代码剖析](#) 一文。

### <a name="pA0">package.json</a>
这个 json 文件包含了当前教材的缩略信息，共有如下属性及嵌套属性：

- 基本属性：`name` , `uuid` , `cover`
- 复杂属性：
	+ `modules` ：存了四个章节的信息，放在一个数组中的4个对象，每个对象有 `name` 和所包含的单元 `units` 属性
	+ `units` ：总共包含8个单元，也就是一个数组里存放了8个对象，每个对象又有三个属性，即 `name` , `title` , 以及包含的 `cards`
