## 基础类型

### 布尔值

布尔值类型在ts中是`boolean`

```typescript
let isDone: boolean = false
```

### 数字

ts中的所有数字都是浮点数，类型是`number`，支持二、八、十、十六进制

```typescript
let decLiteral: number = 6
let hexLiteral: number = 0xf00d
let binaryLiteral: number = 0b1010
let octalLiteral: number = 0o744
```

### 字符串

ts中字符串类型是`string`，可用''、""和``定义

```typescript
let name: string = 'bob'
name = 'zzz'
let age: number = 37
let sentence: string = `hello, my name is ${name}.

I'll be ${age+1} years old next month.`
```

### 数组

ts的数组有两种定义方式

```typescript
let list: number[] = [1, 2, 3]
let list: Array<number> = [1, 2, 3]
```

如果数组中的元素有多种数据类型，则需要通过联合类型来声明

```typescript
let list: Array<string | number> = [1, '2']
let list: any[] = [1, '2']
```

### 元组

元组类型允许表示一个已知元素数量和类型的数组

```typescript
let x:[string, number]
x = ['1', 2]
```

元组长度在2.7版本后被固定，访问元组越界元素会抱回

```typescript
let tuple: [number, string] = [1, 'a']
tuple.push(2)
console.log(tuple) // [1, 'a', 2]
tuple[2] // 报错，访问越界元素
```

### 枚举

使用枚举类型可以为一组数值赋予友好的名字

```typescript
enum Color {Red, Green, Blue}
let c: Color = Color.Green
console.log(c) // 1
```

默认情况下，枚举是从0开始的，也可以手动指定元素编号

```typescript
enum Color {Red=1, Green, Blue=5}
let c:Color = Color.blue
console.log(c) // 5
```

枚举类型提供的一个便利就是可以通过枚举的值来得到枚举的名字

```typescript
enum Color {Red=1, Green, Blue=5}
let c:String = Color[5]
console.log(c) // Blue
```

### Any

有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型，那么就可以用any来标记这个变量

```typescript
let notSure: any = 4
notSure = 'maybe a string instead'
notSure = false
```

any类型允许在编译时可选择地包含或移除类型检查。和object的区别在于，object类型的变量这是允许给它赋任意值，但不能够在上面调用任意方法。

### Void

void类型和any相反，表示没有任何类型。当函数没有返回值，则函数的返回值类型是void

```typescript
function warnUser():void{
	console.log('this is my warning message')
}
```

void类型的变量值只能是undefined和null

```typescript
let unusable: void = undefined
```

### Null和Undefined

默认情况下null和undefined是所有类型的子类型，这两个值可以赋值给任何类型的变量

```typescript
let u: undefined = undefined
let n: null = null
let num: number = u
```

当你指定了`--strictNullChecks`标记，`null`和`undefined`只能赋值给`void`和它们各自

### Never

`never`类型表示的是那些永不存在的值的类型。

`never`类型是任何类型的子类型，也可以赋值给任何类型；*没有*类型是`never`的子类型或可以赋值给`never`类型（除了`never`本身之外）。 即使 `any`也不可以赋值给`never`。

```typescript
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
    return error("Something failed");
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
```

### Object

object表示非原始类型，也就是除`number`，`string`，`boolean`，`symbol`，`null`或`undefined`之外的类型。

```typescript
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error
```

### 类型断言

通过*类型断言*这种方式可以告诉编译器，“相信我，我知道自己在干什么”。 类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 ts会假设你，程序员，已经进行了必须的检查。

类型断言有两种方式。其一时尖括号语法：

```typescript
let someValue: any = 'this is a string'
let strLength: number = (<string>someValue).length
```

另一种是as语法：

```typescript
let someValue: any = 'this is a string'
let strLength: number = (someValue as string).length
```

JSX中只有as语法断言是允许的