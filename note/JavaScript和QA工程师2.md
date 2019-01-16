#### JavaScript和QA工程师 2

最近正在学习前端测试的知识，接触了
[Karma](http://karma-runner.github.io/3.0/intro/installation.html) 、
[Selenium-webdriver](https://www.npmjs.com/package/selenium-webdriver)、
[Nightwatch.js](http://nightwatchjs.org/)、
[Jest](https://jestjs.io/zh-Hans/)、
[Rize](https://rize.js.org/zh-CN/)、
[F2etest](https://github.com/alibaba/f2etest)、
[PhantomCSS](https://www.npmjs.com/package/phantomcss)、
[BackstopJS](https://github.com/garris/BackstopJS)、
[Mocha](https://mochajs.org/)

#### 单元测试

使用karma集成测试环境对代码进行单元测试
```bash
# 在安装karma之前，最起码要有一个文件夹
# 然后我们需要用到我们的包管理工具，创建一个package.json, 我这里执行默认行为
$ npm init -y 

# 安装 Karma:
$ npm install karma --save-dev

# 安装项目所需要的插件
$ npm install karma-jasmine karma-phantomjs-launcher jasmine-core --save-dev

# 使用 karma init初始化（我这里将karma init放到 npm script中）
# 初始化中主要是要选择测试框架 jasmine 和浏览器 PhantomJS 
```

然后再安装一个代码覆盖率报表的插件 [karma-coverage](https://github.com/karma-runner/karma-coverage)
```
npm install karma-coverage --save-dev
```
```js
// Karma 初始化结束后  项目中会生成一个karma.conf.js 配置文件文件 

module.exports = function(config) {
  config.set({

    // 将用于解析所有模式的基本路径
    // 如果basePath配置是相对路径，则它将被解析为配置文件的__dirname。
    basePath: '',

    // frameworks to use
    // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
    frameworks: ['jasmine'],

    // 需要在浏览器中加载的文件/模式列表
    files: [
      "./unit/**/*.js",
      "./unit/**/*.spec.js"
    ],

    // 要排除的文件/模式列表
    exclude: [
    ],

    // 在将文件提供给浏览器之前预处理匹配文件
    // 可用的预处理器：https://npmjs.org/browse/keyword/karma-preprocessor
    // 注意：几乎所有Karma中的预处理器（除了CoffeeScript和其他一些默认值）都需要安装一个额外的库（通过NPM）

    preprocessors: {
      'unit/**/*.js': ['coverage']
    },

    // 报表生成的dir
    coverageReporter: {
      type : 'html',
      dir : './docs/coverage/'
    },

    // 测试结果使用报表
    // 可能的值：'dots', 'progress'
    // 可用的报表：https://npmjs.org/browse/keyword/karma-reporter
    
    reporters: ['progress','coverage'],


    // web server port
    port: 9876,


    // 启用/禁用输出中的颜色（报表和日志）
    colors: true,

    // 日志级别
    // 可能的值：config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
    logLevel: config.LOG_INFO,

    // 启用/禁用观察文件并在文件更改时执行测试
    autoWatch: false,

    // start these browsers
    // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
    // Chrome （需要karma-chrome-launcher插件）
    // ChromeCanary （需要karma-chrome-launcher插件）
    // ChromeHeadless （需要karma-chrome-launcher插件^ 2.1.0）
    // PhantomJS （需要karma-phantomjs-launcher插件）
    // Firefox （需要karma-firefox-launcher插件）
    // Opera （需要karma-opera-launcher插件）
    // IE （需要karma-ie-launcher插件）
    // Safari （需要karma-safari-launcher插件）
    browsers: ['PhantomJS'],

    // 如果为true，Karma将启动并捕获所有已配置的浏览器，运行测试然后以退出代码0或1退出，具体取决于是否所有测试都通过或任何测试失败。
    singleRun: true,

    // Karma应该同时启动多少个浏览器
    concurrency: Infinity
  })
}
```

上面的配置文件中，我选择unit作为浏览器加载的dir，docs作为报表生成的dir，所以我们需要一个unit文件夹放测试文件，并创建index.js、index.spec.js

```js
// index.js
window.add = function (a) {
    if (a==1) {
        return 1;
    } else {
        return a + 1;
    }
}
```
```js
// index.spec.js
describe("项目描述：测试基本函数的API", function() {
    it("对应的测试用例：+1函数应用", function() {
        expect(window.add(1)).toBe(1);
        expect(window.add(2)).toBe(3);
    })
})
```

然后启动karma, 可以看到终端段缘测试的结果了
```
karma start
```
然后docs中生成一个coverage文件夹，里面有生成的测试代码覆盖率的可视化html页面。

npm小知识：
  - 生命周期pre和post，（prestrat，strat，poststrat命令都存在） npm run strat时自动执行 prestrat, strat, poststrat
  - npm script 管道|、串行&&、并行&



#### 自动化测试 e2e

selenium-webdriver 安装
```
npm install selenium-webdriver --save-dev
``` 
创建一个e2e文件夹，再创建子文件夹sel放用来放selenium-webdriver代码，rize文件放rize代码，自动化测试的代码, 再新建selenium-webdriver代码google.spec.js，rize代码github.spec.js
```js
// google.spec.js
const {Builder, By, Key, until} = require('selenium-webdriver');

(async function example() {
  // 需要下载其他组件才能使用每个主流浏览器
  let driver = await new Builder().forBrowser('chrome').build();
  try {
    await driver.get('http://www.google.com/ncr');
    await driver.findElement(By.name('q')).sendKeys('webdriver', Key.RETURN);
    await driver.wait(until.titleIs('webdriver - Google Search'), 3000);
  } finally {
    await driver.quit(); 
    // 自动化测试可以检测到不正常退出，然后告诉你 代码正常退出0或1非正常退出 
  }
})();
```
每个主流浏览器都需要下载其他组件才能使用,解压后放到项目根目录中，比如chrome，本地安装chrome了，还需要 chromedriver(.exe)，safari还需要配置 `工具栏 -> 开发-> 允许远程自动化`

| Browser           	| Component                                                                                                                                                                            	|
|-------------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Chrome            	| [chromedriver(.exe)](http://chromedriver.storage.googleapis.com/index.html)                                                                                                          	|
| Internet Explorer 	| [IEDriverServer.exe](http://selenium-release.storage.googleapis.com/index.html)                                                                                                      	|
| Edge              	| [MicrosoftWebDriver.msi](https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/)                                                                                      	|
| Firefox           	| [geckodriver(.exe)](https://github.com/mozilla/geckodriver/releases/)                                                                                                                	|
| Safari            	| [safaridriver](https://developer.apple.com/library/archive/releasenotes/General/WhatsNewInSafari/Articles/Safari_10_0.html#//apple_ref/doc/uid/TP40014305-CH11-DontLinkElementID_28) 	|

因为我选择的是谷歌浏览器，所以还需要安装谷歌的启动器，放到当前项目根目录，然后运行`node ./e2e/sel/*.spec.js`


Rize安装
```
npm install --save-dev puppeteer rize
```
Rize 是一个提供顶层的、流畅并且可以链式调用的 API 的库，它能让您简单地使用 puppeteer。
（下面的官方代码仅仅做参考，毕竟偶尔也跑不通，暂时不知道原因）
```js
// github.spec.js
const Rize = require('rize'); // 或者 import Rize from 'rize';
const rize = new Rize();
rize
  .goto('https://github.com/')
  .type('input.header-search-input', 'node')
  .press('Enter')
  .waitForNavigation()
  .assertSee('Node.js')
  .end()  // 别忘了调用 `end` 方法来退出浏览器！
```
然后运行`node ./e2e/rize/*.spec.js`


#### UI测试

[BackstopJS](https://www.npmjs.com/package/backstopjs) 通过比较DOM截图随时间的变化，自动对响应式Web UI进行可视化回归测试

安装BackstopJS
```bash
npm install -g backstopjs

# 假如出现这个错误, ERR! phantomjs-prebuilt@2.1.16 install: `node install.js`
# 那么恭喜你，你可以尝试运行 npm install -g backstopjs --ignore-scripts

# 官方文档 https://docs.npmjs.com/cli/install
# The --ignore-scripts argument will cause npm to not execute any scripts defined in the package.json. See npm-scripts.

# 假如你backstop test 又出现了这个问题 Chromium revision is not downloaded. Run "npm install" or "yarn install" 
# 我目前解决不了, 替代方案是用cnpm 安装backstopjs，个人猜测，可能跟仓库有关系，暂时就先这样流程走起来把，接下来学node再好好研究下npm
```
```js
{
  "id": "backstop_default", // 工程名称
  "viewports": [  // 视窗设置，配置分辨率
    {
      "label": "phone", // 测试用例名
      "width": 375,
      "height": 667
    },
    {
      "label": "tablet",
      "width": 1024,
      "height": 768
    }
  ],
  "onBeforeScript": "puppet/onBefore.js", // 测试之前执行脚本
  "onReadyScript": "puppet/onReady.js", // 运行脚本
  "scenarios": [  // 场景，即测试用例，支持多个测试用例
    {
      "label": "BackstopJS Homepage", // 名称
      "cookiePath": "backstop_data/engine_scripts/cookies.json", // 以JSON格式设置cookie，默认使用onBeforeScript 
      "url": "https://map.qq.com/m/", // 测试的地址
      "referenceUrl": "", // 不同环境的URL，可以指定其他环境地址，感觉可以区别测试和线上。
      "readyEvent": "", // 等待字符串打印在控制台后执行后续步骤
      "readySelector": "",  // 等待该元素存在后执行后续步骤
      "delay": 0, // 等待xx毫秒
      "hideSelectors": [],  // 隐藏元素visibility: hidden，设置可见性的数组列表，即可隐藏多个元素
      "removeSelectors": [],  // 删除元素display: none，为数组列表形式，即可删除多个元素
      "hoverSelector": "",  // 在屏幕截图前将鼠标移至某元素上，默认使用onReadyScript，即需要配置原始onReady.js脚本
      "clickSelector": "",  //在屏幕截图前单击某元素，默认使用onReadyScript，即需要配置原始onReady.js脚本
      "postInteractionWait": 0, // 在与hoverSelector或clickSelector交互之后等待选择器，等待时间xx毫秒,默认onReadyScript，即"puppet/onReady.js"
      "selectors": [],// 指定截图区域，为空则默认为文档，截取整个viewport，为数组列表形式
      "selectorExpansion": true,  // 如果设置为true – selectors尺寸大小的任何更改都会触发测试失败
      "expect": 0,
      "misMatchThreshold" : 0.1,
      "requireSameDimensions": true
    }
  ],
  "paths": {
    "bitmaps_reference": "backstop_data/bitmaps_reference", // 存放测试时的ui给的图片
    "bitmaps_test": "backstop_data/bitmaps_test", // 生成测试时的图片
    "engine_scripts": "backstop_data/engine_scripts", // 测试脚本路径地址
    "html_report": "docs/backstop_data/html_report", // 生成测试的html报表的路径
    "ci_report": "backstop_data/ci_report"
  },
  "report": ["browser"], // 报告形式，数组列表，可以为browser、ci
  "engine": "puppeteer", //  执行方式，即驱动；可以为chrome、chromium、phantomjs等等
  "engineOptions": { // 驱动配置项
    "args": ["--no-sandbox"]
  },
  "asyncCaptureLimit": 5, // 最小并行事务
  "asyncCompareLimit": 50, // 最小匹配事务
  "debug": false, // 是否开启debug
  "debugWindow": false // 是否打印详细信息
}

```

backstop.json配置文件  
backstop_data 是backstop的引擎，用到了三个库casper、chromy、puppet  
backstop_data -> cookie.json，如果网站需要登录，模拟cookie  
casper 核心库，在无头浏览器操作游走的  
chromy 版本内核的操作  
puppet  

#### 接口测试

Mocha是以串行方式运行的异步测试框架 , 可视化报表插件 [mochawesome](https://www.npmjs.com/package/mochawesome)
```
npm install --save-dev mocha
```
创建一个service文件夹，新建router.spec.js文件
```js
// 接口测试文件router.spec.js
const axios = require("axios");
describe("node接口测试", function(){
  it("接口/测试",function(done){
    axios.get("http://localhost:3000/")
    .then(function(response){
      if(response.data.result == "Hello World"){
        done();
      }else{
        console.log(response.data)
        done(new Error("请求接口数据出错"))
      }
    })
    .catch(function(error){
      done(error);
    })
  });
});
```
上面代码需要启动了一个本地服务器，这里我们选择 [express](https://expressjs.com/)作为本地启动的服务框架，还需要安装一下[axio](https://github.com/axios/axios)基于Promise的HTTP服务的插件。
```
npm install express --save-dev
npm install axios --save-dev
```
配置一个本地服务，项目根目录创建一个express.js
```js
var express = require('express')
var app = express()

app.get('/', function (req, res) {
  res.send({
    result: 'Hello World'
  })
})

app.listen(3000)
```
我们先启动本地服务node express.js
现在我们需要把mocha跑起来，在项目根目录再新建一个文件mochaRunner.js
```js
// mochaRunner.js
const Mocha = require("mocha");
const mocha = new Mocha({
  reporter: 'mochawesome',
  reporterOptions: {
      reportDir: "./docs/mochawesome-report" //报表生成dir
  }
})
mocha.addFile("./service/router.spec.js"); // 添加测试文件
mocha.run(function(errLength) {
  if (errLength == 0) {
      process.exit(); // 正常退出
  } else {
      console.log("出错长度", errLength);
      process.exit(1); // 非正常退出
  }
})
```
最后`node ./mochaRunner`，你就可以把接口测试跑起来了，docs里有mochawesome生成的html报表

最后的最后附赠一个三毛钱的小工具npm script命令太长了就可以用
[npm-run-all](https://www.npmjs.com/package/npm-run-all) 减少 npm run 的小工具，run-s用于顺序执行，run-p用于并行
