---
title: TypeScript
---

## Pick

```js
// Pick 类型选择
// 也就在一个符合类型中，取出几个想要的类型进行组合
interface Origin {
  name: string;
  age: number;
}

// 只取 name 属性
type Target = Pick<Origin, 'name'>;

let obj: Target = {
  name: 'typescript',
};
```

## Omit

```js
// Omit 类型省略
// 与 Pick 刚好相反、删除集合中指定的属性

interface Test1 {
  attr1?: string;
  attr2: number;
}

// 将 attr1 属性删除，生成新的集合
type Test2 = Omit<Test, 'attr1'>;

let obj: Test2 = {
  attr2: 123,
};
```

## Exclude

```js
// Exclude 排除类型
type test1 = 'a' | 'b' | 'c';
type test2 = 'a' | 'b' | 't';

// 将 test1 与 test2 相同的部分排除
// 排除后只剩下 `c` 类型
const str: Exclude<test1, test2> = 'c';
```

## Partial

```js
// Partial 部分的
// 将一个集合中所有的必填属性变为可选属性
interface Person {
  name: string;
  age: string;
}

const person: Partial<Person> = {
  age: 18,
};
```

## 扩展 window 对象属性

```js
window.MySpace = window.MySpace || {};
// Property 'MySpace' does not exist on type 'Window & typeof globalThis'.
// 即 Window & typeof globalThis 交叉类型上不存在 MySpace 属性

// 创建 lib.dom.d.ts 文件
declare interface Window {
  MySpace: any;
}

// 这就解决了
window.MySpace = window.MySpace || {};
```
