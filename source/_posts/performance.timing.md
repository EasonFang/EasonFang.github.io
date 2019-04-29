---
title: 浏览器性能接口performance.timing
---
利用window.performance.timing进行性能分析

<!-- more -->
### window.performance.timing中相关属性语义：

``` javascript
//  .navigationStart 准备加载页面的起始时间
//  .unloadEventStart 如果前一个文档和当前文档同源,返回前一个文档开始unload的时间
//  .unloadEventEnd 如果前一个文档和当前文档同源,返回前一个文档开始unload结束的时间
//  .redirectStart   如果有重定向,这里是重定向开始的时间.
//  .redirectEnd     如果有重定向,这里是重定向结束的时间.
//  .fetchStart        开始检查缓存或开始获取资源的时间
//  .domainLookupStart   开始进行dns查询的时间
//  .domainLookupEnd     dns查询结束的时间
//  .connectStart                  开始建立连接请求资源的时间
//  .connectEnd                     建立连接成功的时间.
//  .secureConnectionStart      如果是https请求.返回ssl握手的时间
//  .requestStart                     开始请求文档时间(包括从服务器,本地缓存请求)
//  .responseStart                   接收到第一个字节的时间
//  .responseEnd                      接收到最后一个字节的时间.
//  .domLoading                       ‘current document readiness’ 设置为 loading的时间 (这个时候还木有开始解析文档)
//  .domInteractive               文档解析结束的时间
//  .domContentLoadedEventStart    DOMContentLoaded事件开始的时间
//  .domContentLoadedEventEnd      DOMContentLoaded事件结束的时间
//  .domComplete        current document readiness被设置 complete的时间
//  .loadEventStart      触发onload事件的时间
//  .loadEventEnd       onload事件结束的时间
```
### 主要性能分析指标

```javascript
(function() {
    handleAddListener('load', getTiming)
    function handleAddListener(type, fn) {
        if(window.addEventListener) {
            window.addEventListener(type, fn)
        } else {
            window.attachEvent('on' + type, fn)
        }
    }
    function getTiming() {
        try {
            var time = performance.timing;
            var timingObj = {};
            var loadTime = (time.loadEventEnd - time.loadEventStart) / 1000;
            if(loadTime < 0) {
                setTimeout(function() {
                    getTiming();
                }, 200);
                return;
            }
            timingObj['重定向时间'] = (time.redirectEnd - time.redirectStart) / 1000;
            timingObj['DNS解析时间'] = (time.domainLookupEnd - time.domainLookupStart) / 1000;
            timingObj['TCP完成握手时间'] = (time.connectEnd - time.connectStart) / 1000;
            timingObj['HTTP请求响应完成时间'] = (time.responseEnd - time.requestStart) / 1000;
            timingObj['DOM开始加载前所花费时间'] = (time.responseEnd - time.navigationStart) / 1000;
            timingObj['DOM加载完成时间'] = (time.domComplete - time.domLoading) / 1000;
            timingObj['DOM结构解析完成时间'] = (time.domInteractive - time.domLoading) / 1000;
            timingObj['脚本加载时间'] = (time.domContentLoadedEventEnd - time.domContentLoadedEventStart) / 1000;
            timingObj['onload事件时间'] = (time.loadEventEnd - time.loadEventStart) / 1000;
            timingObj['页面完全加载时间'] = (timingObj['重定向时间'] + timingObj['DNS解析时间'] + timingObj['TCP完成握手时间'] + timingObj['HTTP请求响应完成时间'] + timingObj['DOM结构解析完成时间'] + timingObj['DOM加载完成时间']);
            for(item in timingObj) {
                console.log(item + ":" + timingObj[item] + '毫秒(ms)');
            }
            console.log(performance.timing);
        } catch(e) {
            console.log(timingObj)
            console.log(performance.timing);
        }
    }
})();
```