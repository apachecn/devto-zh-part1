# 等等，我做得对吗？

> 原文：<https://dev.to/damcosset/wait-did-i-do-this-right>

## 简介

这周我必须解决工作中的一个问题。在我们的 meteor 应用程序中，目标是确保某些动作是同步的。如果我们打开两个标签，一个用来存钱，另一个用来买东西。我们需要确保第二次行动不会发生在第一次行动之前。

因此，我们的目标是在 meteor 中构建一个同步队列。队列必须在它自己的模块中，不能从前端直接调用(所以它必须在方法之外)。我真的很纠结。这花了我一天多的时间，但我学到了很多。我用承诺来解决我的问题。我很好奇如果另一个(更好？)解决方案。很可能有更好的方法来做到这一点，所以我想听听你的意见。

## 代码

*设置背景*

*提示史诗音乐*

*展示一个角色的脸，一滴眼泪顺着她的脸颊滚落*

*慢慢将相机转向代码*

增强音乐...

[![](img/b242b701e8a18368a0128513e2b35390.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I2pD9DfU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.alicia-logic.com/capsimages/LOTR_Fell086_ElijahWood.jpg)

#### *前端*

```
//The method called by the front-end, the same one for all activities

Meteor.callPromise( 'handleActivity', data)
    .then( () => {
        // Do something on the front when action is handled on the back
    }
    .catch( err => {
        // Do something on the front when an error occurs
    }) 
```

Enter fullscreen mode Exit fullscreen mode

#### *后端*

##### *流星法手柄活动*

```
Meteor.methods({
    handleActivity( data ){
        return new Promise( ( resolve, reject ) => {
            SyncQueue.add( data )
                .then( () => {
                    // back to front. Everything OK
                    resolve()
                    }
                })
                .catch( err => reject( err )) // Error, back to front
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

*SyncQueue* 是保存同步队列实现的模块的名称。

##### *同步队列*

```
const SyncQueue = ( function(){
    let isProcessing = false
    let _actions = [] // stores actions's data

    const add = data => {
        return new Promise( ( resolve, reject ) => {
            _actions.push([data, resolve, reject])

            if( !isProcessing ){
                isProcessing = true
                return next()
            }
        })
    }

    const next = () => {
        if( _actions.length > 0 ){
            return processAction( _actions.shift() ) //Take the first action 
        } else {
            isProcessing = false
            return false // I don't know... just returning something I guess
        }
    }

    const processAction = action =>Â {
        const [ data, resolve, reject ] = action
        const func = // a function ref I retrieve thanks to the activity type

        func()  //These functions also return promises
            .then( () => {
                resolve() 
                // this is the resolve from the add method 
            })
            .catch(err => {
                reject( err )
                // this is the reject from the add method
            })
            .then( () => {
                // Always runs yes ?
                // Any more data to process ?
                return next()
            }
    }
    return {
        // Only needs to show the add method
        add
    }
})() 
```

Enter fullscreen mode Exit fullscreen mode

好了，你知道了。很高兴我解决了。我不知道这是否优雅，或者是否遵循了适当的最佳实践。我也喜欢我没有使用回调...我想知道我如何用 async/await 做到这一点。我也意识到我对流星知识的缺乏确实是个问题。我想我可以使用内置功能来解决这个问题？

不管怎样，你觉得呢？我把事情复杂化了吗？你会怎么解决？很想听听想法:)