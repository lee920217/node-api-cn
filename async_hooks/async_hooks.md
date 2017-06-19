
> 稳定性: 1 - 试验的

`async_hooks` 模块提供了一个用于注册回调函数的 API，这些回调函数可追踪在 Node.js 应用中创建的异步资源的生命周期。
可以通过以下方式使用：

```js
const async_hooks = require('async_hooks');
```
术语
==============
一个异步资源表现为一个对象和其相关的回调，这个回调可能会多次调用，具体来说，像`connection`事件在`net.CreatServer`中，或者只被调用一次，像`fs.open`，资源也可以在回调被执行之前关闭.`AsyncHook` 并没有在这些Case中做明确的区分，但是会抽象的描述这是一个异步资源.

公用接口
==============
概述
--------------
```js
下面通过一些简单的例子来看一下这些接口:
const async_hooks = require('async_hooks');
// Return the ID of the current execution context.
const cid = async_hooks.currentId();

// Return the ID of the handle responsible for triggering the callback of the
// current execution scope to call.
const tid = async_hooks.triggerId();

// Create a new AsyncHook instance. All of these callbacks are optional.
const asyncHook = async_hooks.createHook({ init, before, after, destroy });

// Allow callbacks of this AsyncHook instance to call. This is not an implicit
// action after running the constructor, and must be explicitly run to begin
// executing callbacks.
asyncHook.enable();

// Disable listening for new asynchronous events.
asyncHook.disable();

//
// The following are the callbacks that can be passed to createHook().
//

// init is called during object construction. The resource may not have
// completed construction when this callback runs, therefore all fields of the
// resource referenced by "asyncId" may not have been populated.
function init(asyncId, type, triggerId, resource) { }

// before is called just before the resource's callback is called. It can be
// called 0-N times for handles (e.g. TCPWrap), and will be called exactly 1
// time for requests (e.g. FSReqWrap).
function before(asyncId) { }

// after is called just after the resource's callback has finished.
function after(asyncId) { }

// destroy is called when an AsyncWrap instance is destroyed.
function destroy(asyncId) { }
```
async_hooks.createHook(callbacks)
---------------------------------
v 8.1.0 新增
`callbacks` <object> 注册回调函数
`Returns`: {AsyncHook} 用于禁用或者使用Hook
为每个生命周期不同的异步操作注册方法
