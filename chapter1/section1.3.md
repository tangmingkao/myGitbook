#### Promise 的实现

```javasctipt
//promise 的链式调用
// 1. 如果then方法中（成功或者失败）如果返回的不是一个promise。会将这个结果值传递给外层下一次的then的成功结果中
// 2. 如果执行then方法中的方法出错啦，抛出异常 走到下一个的失败中
// 3. 如果返回的是promise的结果会作为下一次then的成功或者失败。

//什么情况会失败
// 1. 出错会失败
// 2. 返回的promise出错会失败

//错误处理，处理是找最近的错误处理方法
//catch 就是then的别名 没有成功只要失败回调函数 （找最近的错误处理函数优先处理，处理不了找下一个最近的错误处理函数）

const STATUS = {
    PADDING: 'PADDING',
    FUFILLED: 'FUFILLED',
    REJECTED: 'REJECTED',
};

// 1. 如果x是promise，则要采用他的状态
// 2. 如果x是普通值，则直接resolve(x)
function resolvePromise(x, promise2, resolve, reject) {
    //如果是相同的则抛出一个类型错误
    if (promise2 == x) {
        reject(new TypeError('something wrong'));
    }
    if (typeof x === 'object' && x != null || typeof x === 'function') {
        let called;
        try {
            let then = x.then;
            if (typeof then === 'function') {
                then.call(x, function (y) {
                    if (called) return;
                    called = true;
                    resolvePromise(y, promise2, resolve, reject);
                }, function (r) {
                    if (called) return;
                    called = true;
                    reject(r);
                });
            } else {
                resolve(x);
            }
        } catch (error) {
            if (called) return;
            called = true;
            reject(error);
        }

    } else {
        resolve(x);
    }
}

//判断是否是promise
function isPromise(val) {
    return typeof val.then == 'function';
}
function processData() {
    
}
// promise 的executor 默认执行
class Promise {
    constructor(executor) {
        this.status = STATUS.PADDING;
        this.value = undefined;
        this.reason = undefined;
        this.onResolvedCallbacks = [];
        this.onRejectCallbacks = [];
        //当在executor中的同步或者异步方法执行结果触发时。会调用缓存列表中的事件。 发布
        const resolve = (val) => {
            //如果是promise 就继续递归解析
            if (val instanceof Promise) {
                return val.then(resolve, reject);
            }
            if (this.status === STATUS.PADDING) {
                this.status = STATUS.FUFILLED;
                this.value = val;
                this.onResolvedCallbacks.forEach(fn => {
                    fn();
                });
            }
        };
        const reject = (reason) => {
            if (this.status === STATUS.PADDING) {
                this.status = STATUS.REJECTED;
                this.reason = reason;
                this.onRejectCallbacks.forEach(fn => {
                    fn();
                });
            }
        }
        try {
            //默认执行executo。在executor中传人2个回调函数。当前实例化promise是会在executor内部的
            //同步或者异步方法执行结果时触发。
            executor(resolve, reject);
        } catch (error) {
            reject(error);
        }
    }
    //then方法的核心实现就是利用发布订阅设计模式
    // 1. 如果当前状态是FUFILLED，则直接执行success函数
    // 2. 如果当前状态是REJECTED，则直接执行fail函数
    // 3. 如果当前状态是PADDING，则将sucess函数喝fail函数分别放到事件缓存队列中 订阅

    //then能链式调用的的原因： 每次调用then方法都返回一个新的promise

    //判断返回值和promise2的关系。
    then(onFulfilled, onRejected) {
        // 可选参数 处理参数
        onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : x => x
        onRejected = typeof onRejected === 'function' ? onRejected : err => {
            throw err
        }
        let promise2 = new Promise((resolve, reject) => {
            if (this.status === STATUS.FUFILLED) {
                setTimeout(() => {
                    try {
                        let x = onFulFiled(this.value);
                        resolvePromise(x, promise2, resolve, reject);
                    } catch (error) {
                        reject(error);
                    }
                }, 0);
            }
            if (this.status === STATUS.REJECTED) {
                setTimeout(() => {
                    try {
                        let x = onRejected(this.reason);
                        resolvePromise(x, promise2, resolve, reject);
                    } catch (error) {
                        reject(error);
                    }
                }, 0);

            }
            if (this.status === STATUS.PADDING) {
                this.onResolvedCallbacks.push(() => {
                    setTimeout(() => {
                        try {
                            let x = onFulfilled(this.value);
                            resolvePromise(x, promise2, resolve, reject);
                        } catch (error) {
                            reject(error);
                        }
                    }, 0);
                });
                this.onRejectCallbacks.push(() => {
                    setTimeout(() => {
                        try {
                            let x = onRejected(this.reason);
                            resolvePromise(x, promise2, resolve, reject);
                        } catch (error) {
                            reject(error);
                        }
                    }, 0);
                });
            }
        });
        return promise2;
    }
    catch (onRejected) {
        return this.then(undefined, onRejected);
    }
    //默认只有成功没有失败
    static resolve(val) {
        return new Promise((resolve, reject) => {
            resolve(val);
        });
    }
    //失败的promise
    static reject(reason) {
        return new Promise((resolve, reject) => {
            reject(reason);
        });
    }
    //all 方法
    static all(promises) {
        return new Promise((resolve, reject) => {
            let result = [];
            for (let i = 0; i < promises.length; i++) {
                let p = promises[i];
                if (isPromise(p)) {
                    p.then(data => {
                        //如果是普通值
                        processData(i, data);
                    })
                } else {
                    //
                    processData(i, p);
                }
            }
        });
    }
    //race 方法
    static race() {

    }
}

module.exports = Promise;
```