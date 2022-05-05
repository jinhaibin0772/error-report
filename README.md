# 前端异常上报

> 简介：从简单性、可测试性和松耦合性角度而言，绝大部分**前端开发者**都可以从error-report中受益。解决前端异常信息统一处理的痛点，error-report给复杂的前端异常上报带来了春天。记录前端异常信息，支持断网暂存异常，在线后自动上传暂存异常信息。涵盖 Vue 异常、Axios 异常、原生Ajax 异常、JS 抛出的异常、Promise 异常、Async 异常、加载第三方CDN资源异常等，几乎涵盖了前端所有能涉及到的异常。

## 特点
* 可拔插
* 代码侵入量小
* 使用灵活方便

### 使用
```
import ErrorReport from "./plugins/errorReport";

Vue.use(ErrorReport, {
    reportUrl: "http://localhost:10300/errorReport",
    env: "dev",
    appId: "error-report-5c6pz3e4il59k2f3b6",
    appName: "error-report"
});
```

## 配置参数 options

属性|说明|类型|默认值|是否可以为空
--|:--:|--:|--:|--:
reportUrl|异常上报地址|String|http://localhost:10300/errorReport|N|
delayTime|延时上报Error时间|Number|3000 (单位：毫秒)|Y
appId|项目ID|String||Y
appName|项目名称|String||Y
browser|浏览器名称|String|内部方法可以获取|N|
device|设备名称|String|内部方法可以获取|N|
userId|userId|String||Y|
token|token|String||Y|
timeSpan|发送数据时的时间戳|Number|每次取当前的时间戳|Y|
infoType|信息类别，默认为error|String|type|Y|
msg|错误的具体信息|String|错误的具体信息|Y|
userAgent|userAgent|String|userAgent|Y|
pageUrl|上报页面地址|String|window.location.href|Y|
stack|错误堆栈信息|String|错误堆栈信息|Y|
localStorageKey|建议使用固定的key,下次用户打开浏览器可以直接恢复异常数据并上传|String|localStorageKey|N|
env|环境：dev、test、uat、pro|String|开发环境|Y|
data|更多错误信息|Object|更多错误信息|Y|
