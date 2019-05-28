# 前端异常上报

> 简介：从简单性、可测试性和松耦合性角度而言，绝大部分**前端开发者**都可以从error-report中受益。解决前端异常信息统一处理的痛点，error-report给复杂的前端异常上报带来了春天。记录前端异常信息，支持断网暂存异常，在线后自动上传暂存异常信息。涵盖 Vue 异常、Axios 异常、原生Ajax 异常、JS 抛出的异常、Promise 异常、Async 异常、加载第三方CDN资源异常等，几乎涵盖了前端所有能涉及到的异常。

* 目的：解决前端异常信息统一处理的痛点
* 功能：记录**前端**异常信息，支持断网暂存异常，在线后自动上传暂存异常信息
* 范围：任何前端应用
* 使用：两行代码搞定，使用的复杂度几乎降低到了零

## 特点
* 可拔插
* 代码侵入量小
* 使用灵活方便

## 快速启动

``` bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install
npm run dev
```
此时，浏览器打开，输入网址http://localhost:10300, 进入 **异常上报测试页面**。

### 使用
```
import ErrorReport from "./plugins/errorReport";

Vue.use(ErrorReport, {
    reportUrl: "http://localhost:10300/errorReport",
    appId: "error-report-5c6pz3e4il59k2f3b6",
    appName: "error-report"
});
```

### Demo
[examples](https://github.com/sky9102/error-report/blob/master/src/views/errorTest.vue)

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
level|日志错误级别，如warning, error, info, debug|String|level|Y|
msg|错误的具体信息|String|错误的具体信息|Y|
userAgent|userAgent|String|userAgent|Y|
pageUrl|上报页面地址|String|window.location.href|Y|
stack|错误堆栈信息|String|错误堆栈信息|Y|
localStorageKey|建议使用固定的key,下次用户打开浏览器可以直接恢复异常数据并上传|String|localStorageKey|N|
data|更多错误信息|Object|更多错误信息|Y|


## 注意事项
> 考虑到有些项目使用原生Ajax，Ajax 异常做了原生的拦截，Axios 异常也做了拦截；使用了Axios的童鞋，会出现异常上报两次（原因：Axios 异常拦截器一次，原生Ajax异常 拦截一次），不想上报两次可以选择注释以下代码（***Axios 异常监控***、***Ajax 监控*** 其中任何一个即可）。
在[error-report/src/plugins/errorReport.js](https://github.com/sky9102/error-report/blob/master/src/plugins/errorReport.js)中。

 注释 ***Axios 异常监控***，Axios异常 将不会被上报；
```
        // Axios 异常监控
        axios.interceptors.response.use(null, error => {
            this.options.msg = error.message;
            this.options.stack = this.processStackMsg(error);
            this.options.data = JSON.stringify({
                category: "Axios"
            });

            // 合并上报的数据，包括默认上报的数据和自定义上报的数据
            const reportData = Object.assign({}, this.options);
            this.saveReport(reportData);

            return Promise.reject(error);
        });
```

或者注释掉原生的 ***Ajax 监控*** ，原生Ajax异常 将不会被上报。
```
        // Ajax监控
        const ajaxListener = {};
        // 复制send方法
        ajaxListener.tempSend = XMLHttpRequest.prototype.send;
        // 复制open方法
        ajaxListener.tempOpen = XMLHttpRequest.prototype.open;
        // 重写open方法,记录请求的url
        XMLHttpRequest.prototype.open = function(method, url, boolen) {
            ajaxListener.tempOpen.apply(this, [method, url, boolen]);
            this.ajaxUrl = url;
        };
        const self = this;
        // 发送
        XMLHttpRequest.prototype.send = function(data) {
            const tempReadystate = this.onreadystatechange;
            this.onreadystatechange = function() {
                if (this.readyState === 4) {
                    if (this.status >= 200 && this.status < 300) {
                        tempReadystate && tempReadystate.apply(this, [data]);
                        return;
                    }

                    self.options.msg = "AJAX 请求错误";
                    self.options.stack = `错误码：${this.status}`;
                    self.options.data = JSON.stringify({
                        requestUrl: this.ajaxUrl,
                        category: "XMLHttpRequest",
                        text: this.statusText,
                        status: this.status
                    });
                    // 合并上报的数据，包括默认上报的数据和自定义上报的数据
                    const reportData = Object.assign({}, self.options);
                    // 把错误信息发送给后台
                    self.saveReport(reportData);
                }
            };

            ajaxListener.tempSend.apply(this, [data]);
        };
```
**具体情况，看需求！**

**具体情况，看需求！**

**具体情况，看需求！**

**重要事情说三遍！！！**


## 警告
* 本项目仅用于学习练习
* 本项目还不完善，仍处在开发中，不承担任何使用后果
* 本项目代码开源[MIT](https://github.com/linlinjava/litemall/blob/master/LICENSE)，项目文档采用 [署名-禁止演绎 4.0 国际协议许可](https://creativecommons.org/licenses/by-nd/4.0/deed.zh)


## 问题
* 开发者有问题或者好的建议可以用[Issues](https://github.com/sky9102/error-report/issues)反馈交流，请给出详细信息。
* 如果有问题需要提问，请在提问前先完成以下过程：
    * 请仔细阅读本项目文档，查看能否解决；
    * 请提问前尽可能做一些DEBUG或者思考分析，然后提问时给出详细的错误相关信息以及个人对问题的理解。

## License
[MIT](https://github.com/sky9102/error-report/blob/master/LICENSE) Copyright (c) 2019 [sky9102](https://github.com/sky9102)
