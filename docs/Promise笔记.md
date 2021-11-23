# Promise笔记

[其它人详细笔记](https://gitee.com/hongjilin/hongs-study-notes/tree/master/编程_前端开发学习笔记/Promise学习笔记)

- **优势**：解决回调地狱，指定回调函数方式更灵活
- 两个属性：PromiseState PromiseResult
- **promise状态**：
    - pending
    - resolved/fulfilled
    - rejected
    - **状态只改变一次**

![Promise系统学习_promise工作流程](Promise笔记.assets/Promise系统学习_promise工作流程.png)

- **异常穿透**：

    - catch和then中第二个回调函数一起使用，无法继续向下穿透，因为第二个回调函数会导致返回Promise状态为resolved

- **util.promisify**:

    - 可以将函数直接变成promise的封装方式,不用再去手动封装

- **常用方法**：

    - **Promise 构造函数: Promise (excutor) {}**

    - **Promise.prototype.then 方法: (onResolved, onRejected) => {}  返回Promise对象**

        ① 如果抛出异常, 新 promise 变为 rejected, reason 为抛出的异常

        ② 如果返回的是非 promise 的任意值, 新 promise 变为 resolved, value 为返回的值

        ③ 如果返回的是另一个新 promise, 此 promise 的结果就会成为新 promise 的结果

    - **Promise.prototype.catch 方法: (onRejected) => {}**

    - **Promise.resolve 方法: (value) => {}** **返回Promise对象**  规则同上

    - **Promise.reject 方法: (reason) => {}**  **返回Promise对象** 状态失败，结果为传入的东西

    - **Promise.all 方法: (promises) => {}**    **返回Promise对象**

        - 传入Promise对象数组
        - 都成功才成功，一个失败就失败
        - 成功结果为每个Promise结果组成的数组，失败即为失败那个Promise结果

    - **Promise.race 方法: (promises) => {}   返回Promise对象**

        - 传入Promise对象数组
        - 第一个完成的 promise 的结果状态就是最终的结果状态

- **关键问题**

    - 指定多个回调函数都会执行
    - **改变 promise 状态和指定回调函数谁先谁后?**
        - 都有可能   与excutor内代码是否存在异步任务有关

    - **中断 promise 链?**
        - **唯一方式** 在回调函数中返回一个 `pendding` 状态的`promise 对象`

- **async函数**
    - 函数的返回值为 promise 对象
    - promise 对象的结果由 async 函数执行的返回值决定，同三条规则

- **await表达式**
    - await 右侧的表达式一般为 promise 对象, 但也可以是其它的值
    - 如果表达式是 promise 对象, await 返回的是 promise 成功的值
    - 如果表达式是其它值, 直接将此值作为 await 的返回值
    - await 必须写在 async 函数中, 但 async 函数中可以没有 await
    - 如果 await 的 promise 失败了, 就会抛出异常, 需要通过 try...catch 捕获处理

