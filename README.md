# 编程方法论

> **目前 methodology 还在撰写状态，只记录方法，不做详细解释**

## 数据快照

编程（程序设计）将**逻辑**抽象成**数据**，通过**触发事件改变数据**。

例如点击按钮切换按钮颜色

```js
// 初始
{
    btnColor: 'red'
}
// 点击按钮后
{
    btnColor: 'blue'
}
// 再次点击
{
    btnColor: 'red'
}
```

改变数据的过程包含逻辑判断，写出逻辑判断条件和条件对应的数据变动代码。

> 基于 `React/Vue` 的组件实现天然是数据快照的实现（声明式），但某些逻辑封装的库也可以使用数据快照的方式做前期的分析设计。

## 由单个问题中找出它的同类

> 参考 https://github.com/fast-flow/list-logic/issues/3  (翻页（单个问题） `changePage(1)`  翻页的同类 `search({page: 1}))`

解决单个问题，代码适用性不强，且难以找到问题的本质。

找到将多个同类需求用同一个接口实现，逻辑更合理清晰。

## 解耦-减少内部函数

> 解耦，减少依赖。

尽量避免内部函数。虽然定义内部函数后可以不传参直接获取当前函数作用域的所有值，但是会让代码耦合度高。内部函数不可在其他函数中复用。

若独立出一个没有任何依赖的函数，且通过传参的方式传递数据，并明确返回修改后的数据。代码更易于阅读和管理。*传参超过 2 个则使用  `fn({...})` 形式而不是 `fn(a, b ,c ,d ,e)` 这种难以阅读的形式 *

好的例子：
```js
function test(value, rule) {
    return rule.map(function (item) {
        return checkItem(value, item)
    })
}
function checkItem(value, item) {
    var output = {}
    if (item.regexp.test(value) !== item.be) {
        output.error = true
        output.msg = item.msg
    }
    else {
        output.error = false
    }
    return output
}
test(
    'abc',
    [
        {
            regexp: /\d/,
            be: true,
            msg: '必须包含数字'
        }
    ]
)
```

## 分层

```js
Interface = render(
    // UI data
    fill(
        format(
            // Source data
            api(
                ServerData,
                ClientData
            )
        )
    )
)
```
`Source data` + `UI Data` + `render() `

`Source Data` = `api(ServerData, ClientData)`

`UI Data` = `format(SourceData)` + `fill(SourceData)`

将程序设计分为  `Source data` `UI Data` `render()`  并封装 `UI Data` 的逻辑处理代码，可以将复杂的问题简单化。

`Get SourceData => UI Data => Interface(DOM) => Event => api(...) => Update SourceData`

> 如果因为项目迭代原因需要出现重复数据。则在 api 中 `get` 时复制出一份，但我们认为这个数据是不重复的，只是跟另外一个数据一模一样。在 api 中 `set` 要指明修改了谁，由 api 修改成功后返回 3份数据并更新 SourceData。**重复数据管理的复制和关联修改都在 api 中处理**

## 不要着急做到完美

面对复杂的模块开发，先实现最基础的功能保证能解决目前会遇到的业务需求。然后实验一段时间，再次进行重构。

代码库/模块/组件应该经历下面三个阶段

```
1. 简短的函数，最基础的功能。只能满足当前项目的需求
2. 列出所有业务情况，根据业务情况设计接口，不要花太多时间考虑接口的易用性。使用并迭代一段时间，可以不写单元测试
3. 上一个阶段已经经过大量的项目验证并可行后，重新设计接口，接口要求易用性高。能满足目前能遇到的和想象到的所有需求。并有完善的单元测试
```
