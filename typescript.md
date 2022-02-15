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

## 接口

```typescript
interface LabelValue {
  label: string
}

function printLabel(labelObj: LabelValue) {
  console.log(labelObj.label)
}

let myObj = {size: 10, label: 'size 10 object'}
printLabel(myObj)
```

类型检查器去检查传入对象和接口，只关注这两个的外形是否一致，不关注传入对象是否实现了接口

### 可选属性

接口可选属性可以预定义部分属性，可选属性名字定义的后面加一个`?`符号

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  let newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({color: "black"});
```

### 只读属性

只读属性只能在刚刚创建的时候修改值，在属性名前用 `readonly`来指定只读属性

```typescript
interface Point {
  readonly x: number;
  readonly y: number;
}
let p1: Point = {x: 1, y: 2}
console.log(p1.x)
```

`ReadonlyArray<T>`是普通数组去掉可变方法的版本，即使把`ReadonlyArray`赋值给普通数组都不行（可用断言解决）

```typescript
let a: number[] = [1,2,3,4]
let ro: ReadonlyArray<number> = a
// ro[0] = 12 error
// ro.push(5) error
// ro.length = 5 error
// a = ro error
a = ro as number[]
```

### 额外属性检查

像下面那样用可选参数定义接口，调用函数传入参数时有一个额外的属性，这个属性在js中可以通过，但是在typescript中则会额外属性检查，会报错

> 错误原因：对象字面量会被特殊对待而且会经过 *额外属性检查*，当将它们赋值给变量或作为参数传递的时候。 如果一个对象字面量存在任何“目标类型”不包含的属性时，你会得到一个错误

解决方式：

1. 可以用类型断言解决
2. 添加字符串索引签名（最佳方式），前提是会有不确定的额外属性出现
3. 将对象字面量赋值给一个变量再传给函数

```typescript
interface SquareConfig{
  color?: string;
  width?: number
}
function createSquare(config: SquareConfig): {color: string, area: number} {
  return { color: 'white', area: 100}
}
let mySquare = createSquare({colour: 'red', width: 100}) // 报错
let mySquare = createSquare({colour: 'red', width: 100} as SquareConfig) // 解决方式1

// 解决方式2
interface SquareConfig{
  color?: string;
  width?: number
  [propName: string]: any
}

// 解决方式3
let squareOptions = {colour: 'red', width: 100}
let mySquare = createSquare(squareOptions)
```

### 函数类型

接口用一个签名表示函数类型，函数的参数名不需要和接口定义的名字一致

```typescript
interface SearchFunc{
  (source: string, subString: string): boolean
}
let mySearch: SearchFunc = function(src: string, sub: string): boolean{
  let result = src.search(sub)
  return result > -1
}
```

函数的参数会逐个检查，要求对应位置的参数类型和接口匹配。可以不指定函数的参数和返回值类型，typescript会推断出类型

```typescript
interface SearchFunc{
  (source: string, subString: string): boolean
}
let mySearch: SearchFunc = function(src, sub){
  let result = src.search(sub)
  return result > -1
}
```

