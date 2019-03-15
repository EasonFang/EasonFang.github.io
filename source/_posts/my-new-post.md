---
title: 观察者模式
date: 2019-03-14 14:08:52
tags: 设计模式
categories: 前端开发
---

  观察者模式又被称为发布-订阅模型或消息机制。

基本思想是观察者一个静态（或全局）对象，为大家提供三个方法：发布、订阅、取消订阅。
<!-- more -->
想得到消息的订阅者需要通过订阅某些消息，当发布者发布某些消息的时候对应的订阅者就收到消息了。订阅者也可以取消订阅。
```javascript
    var Observer = (function(){
    var _messages = {};
    return {
        regist: function(type,fn) {   //订阅消息
            if(typeof _messages[type] === 'undefined'){
                _messages[type] = [fn];
            } else {
                _messages[type].push(fn);
            }
        },
        fire: function(type,args){     //发布消息
            if(!_messages[type])
                return;
            var events = {
                type: type,
                args: args;
            };

            _messages[type].forEach(function(item){
                item.call(this,events);
            });
        },  
        remove: function(type,args){   //取消订阅
            if(_messages[type] instanceof Array){
                _messages[type].lastIndexOf(fn) && _messages[type].splice(i,1);
                /*var i = _messages[type].length-1;
                for(; i>-1; i--){
                    _messages[type][i] === fn && _messages[type].splice(i,1);
                }*/
            }
        }
    }
})();
```
使用场景，用户在留言评论的同时用户消息栏也相应改变。这里订阅者是评论模块和消息模块，发布者是用户操作模块。
```javascript
//评论模块：
(function(){
    Observer.regist('addCommentMessage',addMsg); 
    Observer.regist('addCommentMessage',removeMsg); 
    function addMsg(){ 
        //...
    }
    function removeMsg(){ 
        //...
    }
})();

//消息模块
(function(){
    Observer.regist('addCommentMessage',changeMsgNum); 
    Observer.regist('removeCommentMessage',changeMsgNum); 
    function changeMsgNum(){
        //...
    }
})();

//用户操作模块
(function(){
    $("#submitBtn").onclick = function(){
        //...
        Observer.fire('addCommentMessage',{
            text: text.value,
            num: 1
        }); 
    };
    $("#deleteBtn").onclick = function(){
        //...
        Observer.fire('removeCommentMessage',{
            num: -1
        }); 
    };
})();
```