
参考文章
> https://medium.com/i-am-mike/vue3-%E8%AE%8A%E6%95%B8%E7%9A%84%E5%AD%98%E5%8F%96%E6%8E%A7%E5%88%B6%E8%88%87%E6%95%88%E8%83%BD%E6%9C%80%E4%BD%B3%E5%8C%96-fdd45a9789ac

---

### vue 针对REF的性能优化方案

#### 以下摘抄自 vue 官方文档
1. shallowRef ref的浅层作用形式。不会被深层递归地转为响应式。
2. ref 在数据量巨大时，深度响应性也会导致不小的性能负担。
3. Vue 提供了 shallowRef() 和 shallowReactive() 来绕开深度响应。
4. 绕开深度响应的代价是：我们现在必须将所有深层级对象视为不可变的，并且只能通过替换整个根状态来触发更新。

看一下官方例子就知道对象不可变是什么意思了
```js
const shallowArray = shallowRef([
  /* 巨大的列表，里面包含深层的对象 */
])

// 这不会触发更新...
shallowArray.value.push(newObject)
// 这才会触发更新
shallowArray.value = [...shallowArray.value, newObject]

// 这不会触发更新...
shallowArray.value[0].foo = 1
// 这才会触发更新
shallowArray.value = [
  {
    ...shallowArray.value[0],
    foo: 1
  },
  ...shallowArray.value.slice(1)
]
```
1. 这里介绍下React中的 useState 运用：
```js
import { useState } from 'react';

function MyComponent() {
  const [age, setAge] = useState(28);
  const [name, setName] = useState('Taylor');
  const [address, setAddress] = useState('Taiwan');
}
```
这相当于定义了一个通用的setState 方法，通过 useState 来创建和修改变量。

结合 vue 文档 和 上面参考文章：
提出问题：
1. 使用 shallowRef 如何解决ref的性能问题？
2. ref还有什么其他问题？

从下面代码可以看出 ref 的另一个问题 - 过度灵活：
  由于 ref value 赋值的灵活性，导致了 activeIdx 在多处地方进行了修改操作。这无疑会增加维护成本和代码可读性。
```js
const route = useRoute();
const activeIdx = ref(0);
const setActiveIdx = (idx) => {
  activeIdx.value = idx;
};
watch(route, (newRoute) => {
    activeIdx.value = newRoute.query.activeId;  // 這邊去修改
});
onMounted(()=> {
  setActiveIdx(route.query.activeId) // 這邊去修改
})

```
就像 php 代码中，我们不推荐直接使用 Model 做修改操作一样，这里也不推荐直接对 ref value 进行修改操作。
那应该如何解决呢？
按照 Php 中Model操作的惯例，我门可以在model 中定义相关操作，你如果想要操做数据，就通过 model 里的方法进行操作。同样在这里我们尝试给 ref 变量定义一个 修改方法，这样就可以避免直接修改 ref value。
```js
const username = ref("");
const setUsername = (name) => username.value = name;
```
这样，我们就可以通过 setUsername 方法来修改 username 的值了。
但是新的问题来了，这是一个变量的情况，如果10个呢？每个都需要定义一个方法？这很显然不合理？而且像赋值这样的操作基本上都是通用的，我们能不能定义一个通用的方法呢？
还记得上面提到过的 React中的 useState 吗？我们可以借鉴一下他的操作，定义一个通用的 setState 方法，通过这个setState 来创建相应的变量。这有点类似于工厂模式。
我们最终要实现的代码是这样的：
```js
const [state, setState] = useState(initialState)
```
传入一个初始变量，然后返回 其 变量名称 和 修改方法 等。
根据上面的需求，我们尝试定义这个 useState：

```js
import { shallowRef } from "vue";
export function useState(baseState) {
  const state = shallowRef(baseState);
  const update = (newValue) => {
    state.value = newValue;
  };
  return [state, update];
}
```
这样，我们就可以使用 useState 来创建和修改变量了。
然后我们看下在代码中如何使用：
```js
const [state, setState] = useState("zhangsan")
const [info, setInfo] = useState({
  name: "zhangsan",
  age: 18
})

```
由于 setInfo 是每次对 value 进行整体修改，所以不会失去相应性。同时因为 shallowRef 的缘故，其性能会更好。

然后看一下在form表单中的应用：
这里定义了一个 form 变量 和 方法：
```js
const [form, setForm] = useStateHooks({
  account: '',
  nickname: '',
  password: '',
  status: 1,
})
```
在表单中使用可能和之前有略微差异：
如果直接像ref一样使用，则不会触发响应式：所以必须在@input中触发setForm方法。
```vue
...
<n-input
    v-model:value="form.account"
    type="text"
    placeholder="请输入账户名称"
    @input="setForm({ ...form, account: $event })"
  />
          ..
```

---

至此，针对 ref 的性能优化方案就介绍完了。


---

如果觉得文章对你有帮助，欢迎关注我的公众号：

如果文章中存在什么问题和建议，欢迎留言。