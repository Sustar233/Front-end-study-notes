以下是优化后的Markdown排版：

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

## vue-router