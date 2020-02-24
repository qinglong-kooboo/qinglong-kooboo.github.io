---
title: typescriptの映射类型
date: 2020-02-24 11:42:19
comments: true #是否可评论
toc: true #是否显示文章目录
categories:  #分类
    - Typescript
tags:   #标签
    - typescript类型
---

映射类型：属于ts高级类型，映射类型提供从旧类型中创建新类型的方式。在映射类型中，新类型用相同的方式转换旧类型的每个属性。

条件类型：和映射类型类似，也是在一个类型基础上，执行某些条件操作，形成的新类型。

已经被记录在标准库的一些常用的工具函数Record，Partial，Pick，Readonly，还有通过infer（后面介绍）实现的ReturnType都是映射类型。

下面通过实现这些函数来进一步理解：

### Record
```
//实现 
type Record<[T extends keyof any]，K> = { 
    [P in T ]: K 
} 
 
// 测试用例 
type Names = 'USA' | 'CHINA' | 'JAP' 
interface country { name: string space: number } 
type Countries = Record<Names, country> 
 
// expect: Countries = { USA: country; CHINA: country; JAP: country }
```

### Partial
```
//实现 
type Partial<T> = { 
    [K in keyof T ]?: T[K] 
} 
 
// 测试用例 
interface Colors { 
    yellow: string 
    red: string 
    black: string 
} 
 
type selectedColors = Partial<Colors> 
 
// expect: selectedColors = { yellow?: string; red?: string; black?: string }
```

### Pick
```
//实现 
type Pick<T，K extends keyof T> = { 
    [P in K]: T[P] 
} 
 
// 测试用例 
interface Colors { 
    yellow: string 
    red: string 
    black: string 
} 
type selectedColor = Pick<Colors, 'yellow'> 
 
// expect: selectedColor = { yellow: string }
```

### Readonly
```
//实现 
type Readonly<T> = { 
    readonly [K in keyof T ]: T[K] 
} 
 
// 测试用例 
interface Colors { 
    yellow: string 
    red: string 
    black: string 
} 
 
type selectedColors = Readonly<Colors> 
 
// expect: selectedColors = { readonly yellow: string; readonly red: string; readonly black: string }
```

### “Infer” 指代extends语句中待推断的类型变量，可以看下面的例子
```
//这段表示如果T可以赋值给(params: P) => any,则ParamsType=P 否则ParamsType=T 
 
type ParamsType<T> = T extends (params: infer P) => any ? P : T 
 
//测试用例 
 
type user = { 
    name: string 
    age: number 
} 
 
type Func = (params: user) => any 
type test = ParamsType<Func> 
 
// expect: test = user
```

### ReturnType
```
//实现 
type ReturnType<T> = T extends (...args: any[]) => infer P ? P : any 
 
// 测试用例 
type user = { 
    name: string 
    age: number 
} 
 
type Func = () => user 
type test = ReturnType<Func> 
 
// expect: test = user
```