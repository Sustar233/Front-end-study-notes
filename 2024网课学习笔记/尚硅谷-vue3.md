
# 创建Vue3工程

```bash
npm create vue@latest
```

## Webpack与Vite的区别

- Webpack：先分析处理所有路由，再bundle，server ready
- Vite：先server ready，根据需求分析路由

## Vue3中的setup函数与this

在Vue3的`setup`函数中，`this`是`undefined`。Vue3开始弱化`this`的使用。

## Ref与Reactive
- Ref：
  - 创建的变量必须通过`.value`来访问（可以使用Volar插件自动添加`.value`）
  - 创建对象时，ref底层使用的是reactive
- Reactive：
  - 重新分配一个新对象会失去响应式（可以使用`Object.assign`去整体替换）

**推荐**：
响应式对象层级较深、表单相关时，推荐使用`reactive`；其余情况使用`ref`。

```javascript
let person = reactive({
  name: 'qwq',
  age: 18
});

let { name, age } = toRefs(person);
let nl = toRef(person, 'age');

// 要解构person中的key-value并保留响应式，需要加toRefs
```

## Computed与Watch

- Computed：计算属性，可以缓存结果，默认只读（不可直接修改）
- Watch：
  - 监视`ref`数据时，默认监视的是地址值。如果想监视内部属性变化，需加入`{ deep: true }`
  - 监视`reactive`数据，默认开启深度监视
  - 监视`ref`或`reactive`定义的对象类型数据中的某个属性时：
    - 若该属性值不是对象类型，需要写成函数形式
    - 若该属性值是对象类型，可直接编写，也可写成函数（可能需要开启deep）
  - 监视多个数据时，放入一个数组。数组中的数据遵守前面的规则
- WatchEffect：不需要明确指出监控的数据，响应式地追踪其依赖，并在依赖更改时重新执行该函数

## Ref获取DOM元素

```html
<h2 ref="title2"></h2>
```

```javascript
let title2 = ref();
console.log(title2.value);

```
## ref和组件
* ref加到子组件标签时，默认无法获取子组件数据（出于保护）
* 可以在子组件添加 defineExpose({ a, b, c })来暴露数据

## TypeScript示例

```typescript
export interface HelloInter {
  id: string;
  name: string;
  age: number;
}

// Vue组件中使用
import { type HelloInter } from '@/types';

let person: HelloInter = { id: 'abc', name: 'zhang', age: 555 };

let personList: Array<HelloInter> = [
  { id: 'abc', name: 'zhang', age: 555 },
  { id: 'abc', name: 'zhang', age: 555 },
];
```

```typescript
// 或者
export type Persons = Array<HelloInter>;
export type Persons = HelloInter[];
// 使用以上两种写法均可

let personList: Persons = [...];
```

## 父组件向子组件传值

```html
<Person a="haha" />
```


```javascript
// 子组件中可以使用以下两种方式来接收：
defineProps(['a'])
let x = defineProps(['a'])
console.log(x.a);

// 接收时限制类型
defineProps<{ list: Persons }>();

// list可不传
defineProps<{ list?: Persons }>();

// 接收list + 限制类型 + 限制必要性 + 指定默认值
withDefaults(defineProps<{ list?: Persons }>(), {
  list: [{ id: '111', name: 'qwq', age: 19 }],
});
```

# Vue 3 生命周期与组件挂载  
  
在 Vue 3 中，生命周期钩子有所变化，不再包含创建前和创建完毕的阶段。在 `<script setup>` 中，相关的逻辑处理相当于在组件的创建阶段进行。  
  
```javascript  
import { onBeforeMount, onMounted, onBeforeUpdate, onUpdated, onBeforeUnmount, onUnmounted } from 'vue'  
  
onBeforeMount(() => {  
  console.log('挂载前')  
})  
  
onMounted(() => {  
  console.log('挂载完毕')  
})  
  
// 更新前  
onBeforeUpdate(() => {  
  // ...  
})  
  
// 更新完毕  
onUpdated(() => {  
  // ...  
})  
  
// 卸载前  
onBeforeUnmount(() => {  
  // ...  
})  
  
// 卸载完毕  
onUnmounted(() => {  
  // ...  
})
注意：先执行子组件的挂载，再执行父组件的挂载。

路由组件与一般组件
路由组件：根据路由规则动态渲染出来的组件，通常放在 pages/ 或 views/ 目录中。
一般组件：通过手写标签方式直接使用的组件，例如 <Demo />，通常放在 components/ 目录中。
当通过点击导航切换路由时，视觉上消失的路由组件默认是被卸载掉的，需要时再重新挂载。

路由器的工作模式
history 模式
优点：URL 更加美观，不带有 #，更接近传统网站 URL。
缺点：后期项目上线时，需要服务器端配合处理路径问题，否则刷新页面可能会出现 404 错误。

javascript
import { createRouter, createWebHistory } from 'vue-router'  
  
const router = createRouter({  
  history: createWebHistory(), // 使用 history 模式  
})
hash 模式（多用于后台项目）
优点：兼容性更好，因为不需要服务器端处理路径问题。
缺点：URL 带有 # 不太美观，且在 SEO 优化方面相对较差。

javascript
import { createRouter, createWebHashHistory } from 'vue-router'  
  
const router = createRouter({  
  history: createWebHashHistory(), // 使用 hash 模式  
})
to 属性的两种写法
在 Vue Router 中，to 属性用于指定导航的目标地址，有两种常见的写法：

静态字符串：例如 to="/home"。
动态绑定对象：例如 :to="{ path: '/about' }" 或 :to="{ name: 'zhuye' }"（可以通过路由名称进行导航）。
选择哪种写法取决于具体需求和场景。使用对象形式可以提供更多的灵活性和功能，例如传递查询参数或编程式导航等。而静态字符串形式则更简洁和直观，适用于简单的导航场景。

路由传参
query
写法1:

html
<router-link :to="`/about/detail?id=${news.id}&title=${news.title}`">qwq</router-link>
写法2:

html
<router-link :to="{  
    path: '/news/detail',  
    query: {  
      id: news.id,  
      title: news.title  
    }  
}">qwq</router-link>
使用参数:

javascript
import { useRoute } from 'vue-router';  
let route = useRoute();  
console.log('@', route);
params
html
:to="`/news/detail/${news.id}/${news.title}/${news.content}`"
路由配置：path: 'detail/:id/:title/:content?'

javascript
import { useRoute } from 'vue-router';  
const route = useRoute();  
console.log(route.params.id);
备注1：传递 params 参数时，若使用 TS 的对象写法，必须使用 name 配置项，不能用 path。
备注2：需要提前在规则中占位。

在 router 配置 props: true 后，会将路由收到的所有 params 参数作为 props 传给路由组件。

函数写法
可以自己决定将 query 或者 params 作为 props 给路由组件：

javascript
props(route) {  
  return route.query;  
}
编程式路由导航
脱离 <RouterLink> 实现路由跳转：

javascript
import { useRouter } from 'vue-router';  
router.push('/news');  
// 或者使用对象写法，与 :to 属性相同  
router.push({ path: '/news' });
重定向配置
javascript
{  
  path: '/',  
  redirect: '/home'  
}
Reactive 和 Ref
javascript
import { reactive, ref } from 'vue';  
  
let obj = reactive({  
  a: 1,  
  b: 2,  
  c: ref(3)  
});  
  
let x = ref(9);  
  
console.log(obj.a);  
console.log(obj.b);  
// 注意，reactive 里面的 ref 不需要 .value，可直接使用  
console.log(obj.c);  
console.log(x.value);
Pinia 集中式状态（数据）管理
类似于 Redux, Vuex, 但这是 Pinia。

javascript
import { defineStore } from 'pinia';  
  
export const useCountStore = defineStore('count', {  
  actions: {  
    increment(value: number) {  
      if (this.sum < 10) {  
        this.sum += value;  
      }  
    }  
  },  
  state() {  
    return {  
      sum: 6  
    };  
  }  
});
在组件中使用：

javascript
import { useCountStore } from '@/store/count';  
import { ref } from 'vue';  
  
const countStore = useCountStore();  
let n = ref(1); // 用户选择的数字  
  
function add() {  
  // 第一种修改方式  
  // countStore.sum += n.value;  
  
  // 第二种修改方式（不推荐直接修改状态，应通过 actions）  
  // countStore.$patch({ sum: 888 });  
  
  // 第三种修改方式（推荐）  
  countStore.increment(n.value);  
}



-
