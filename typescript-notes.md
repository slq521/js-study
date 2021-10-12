### 原始数据类型
**基础类型的可以赋予null和undefined值**

```typescript
const isTrue: boolean = true;
const isCreateBoolean = new Boolean(1);
const ages: number = 12;
const createAges: Number = new Number('12');
const names: string = 'slq';
const createNames: String = new String('slq');
const bigNames: bigint = BigInt(1345323243546754321);
const keys: symbol = Symbol('key');
const isNull: null = null;
const isUndefined: undefined = undefined;
```

**任意值（尽量避免用任意值来表示变量的类型）**

```typescript
let changeName: any = 'slq';
changeName = 2345;
changeName = true;
```

**类型推论**
下面语句表示（let test: string = 'test';）
```typescript
let testName = 'test';
// 如果定义时没有赋值，不管以后有没有赋值，都会被推断出为any；
let testAny;
testAny = 123;
testAny = true;
```


**联合类型**
- 用|来分割每个类型
```typescript
let unionName: string|number;
unionName = 121212;
unionName = 'Union Name';
```

- 只能访问联合类型的所有类型共有的属性方法
```typescript
function getNameLength(name: string|number):number {
  // 因为number没有length属性，所以ts检测会报错
  // return name.length;
  return ('' + name).length;
}
```

- 联合类型的变量在被赋值的时候
```typescript
let someTypes: string|boolean|number = 'slq';
someTypes = 121;
```

接下来取someTypes的length属性会报错（上一行ts已经推断出为number类型）
console.log(someTypes.length)


- 对象类型的接口(interface)
定义接口是建议加“I”前缀
```typescript
interface InameObj {
  name: string;
  age: number;
}
// 定义变量比接口少了(如：age不定义的话就会报错)或者多了(如: 多加一个属性color的话就会报错)一些是不允许的
const nameObj: InameObj = {
  name: 'slq',
  age: 18
  // color: 'orange'
}

// 我们如果想不要完全匹配所规定的类型，那么可以选用可选属性;
interface ItestObj {
  name: string;
  age: number;
  color?: string;
}
// 此时color不定义ts编译时也不会报错
const testObj = {
  name: 'slq',
  age: '17',
  color: 'orange',
}

// 任意的属性
// 如果有时我们希望接口允许有任意的属性，可以使用如下的方式
interface IhasAnyProps {
  name: string;
  age: number;
[propsName: string]: any;
}
const hasAnyProps: IhasAnyProps = {
  name: 'slq',
  age: 18,
  other: 'other'
}
// 注意：一旦定义了任意属性，那么确定的类型和可选的类型必须是它类型的字类型
interface IchilderObj {
  name: string;
  age: number;
[propsName: string]: string|number;
  // age是任意属性的子类型，因为类型不一样，所有ts会报错
  // [propName: string]: string,
}
```

- 只读属性(添加readonly前缀)
```typescript
interface Ireadonly {
  readonly name: string;
  age: number;
}
const readonlys: Ireadonly = {
  name: 'slq',
  age: 12,
}
// 下面语句ts编译时会报错
// readonlys.name = 'sdfgh';
```

- 数组类型
```typescript
// 类型加方括号表示
const testArray: number[] = [1, 2, 3, 4, 5, 6];
// 如下：参杂其他类型会报错
// let testArray: number[] = [1, 2, 3, 4, 5, 6, '7'];
// testArray.push('7');

// 数组泛型
const fiArray: Array<number> = [1, 2, 3, 4, 5, 6];

// 用接口表示数组(常用来表示类数组；比如：arguments)
interface IarrayObj {
[index: number]: number;
}
const arraylist: IarrayObj = [1, 2, 3, 4, 5, 6];

// 类数组
interface Iarguments {
[index: number]: any;
  length: number;
  callee: Function;
}
function sum() {
  const arg: Iarguments = arguments;
  // IArguments 是 TypeScript 中定义好了的类型
  // const args: IArguments = arguments;
}

// any在数组中的应用
const anyArrays: any[] = [1, '2', { name: 'slq' }];
```

- 函数的类型（一个函数有输入和输出，要在 TypeScript 中对其进行约束，需要把输入和输出都考虑到）
函数申明（函数申明、函数表达式）
函数(注意: 输入或多或少的参数是不被允许的)
```typescript
function getMessages(name: string, age: number): string {
  return `${name} - ${age}`;
}
// 函数表达式
// 注意：‘=>’在ts中用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。
// 在ES6中 ‘=>’ 表示箭头函数
/**
 * const messages: (name: string, age: number) => string = function(name: string, age: number): string {
 *   return `${name} - ${age}`;
 * }
 **/
 interface Imessages {
    (name: string, age: number): string;
 }
 let messages: Imessages;
 messages = function (name: string, age: number): string {
    return `${name} - ${age}`;
 }

// 定义可选参数(可选参数后面不允许再出现必需参数了)
function hasSelectFn(name: string, age?: number): void {
  console.log(`${name} - ${age}`);
}

// 参数默认值（TypeScript 会将添加了默认值的参数识别为可选参数: 给定默认值后就不受可选参数后面不能存在必须参数的限制了）
function defaultValueFn(value: string = 'void', key: string): void {
  console.log(value, key);
}

// 剩余参数
function restFn(arrays: any[], ...rest: any[]): void {
  // do something
}

// 重载
// 场景： 参数输入数字的时候：返回number: 参数；参数输入字符串时： 返回： string： 参数
// 前两个getpropsTypes可以精准表示参数类型和函数返回一一对应的关系
function getpropsTypes(name: string): string;
function getpropsTypes(name: number): number;
function getpropsTypes(name: string|number): string|number {
  if (typeof name === 'string') {
    return `string: ${name}`;
  }
  return `number: ${name}`;
}
```

### 类型断言
类型断言可以手动指定一个值的类型
- 语法
```javaScript
// 值 as 类型（tsx中使用，建议都是用这个）
// 或者
// <类型>值
```
> 注意：断言只能欺骗ts编译器，无法避免运行时的报错
```javaScript
interface Cat {
  name: string;
  run(): void;
}
interface Fish {
  name: string;
  swim(): void;
}
// 添加断言，不然会报错
function isFish(animal: Cat | Fish) {
  if (typeof (animal as Fish).swim === 'function') {
      return true;
  }
  return false;
}
```
- 将父类断言为更具体的子类
```javaScript
interface ApiError extends Error {
    code: number;
}
interface HttpError extends Error {
    statusCode: number;
}

function isApiError(error: Error) {
    if (typeof (error as ApiError).code === 'number') {
        return true;
    }
    return false;
}
```

- 将任何一个类型断言为any
```javaScript
const foo: number = 12;
// 下面语句ts会报错
// foo.length = 1;
```
场景：在window对象中添加一属性
```javaScript
// ts检测到window对象中没有foo属性，所以会报错
// window.foo = 1;

// 通过any断言，修复报错
(window as any).foo = 1;
```
> 注意： any是解决ts类型问题的最后手段（尽量少用any）
```javaScript
function getCacheData(key: string): any {
    return (window as any).cache[key];
}

interface Cat {
  name: string;
  run(): void;
}

const tom = getCacheData('tom') as Cat;
tom.run();
```

- 类型断言限制
若 A 兼容 B，那么 A 能够被断言为 B，B 也能被断言为 A
```javaScript
interface Animal {
    name: string;
}
interface Cat {
    name: string;
    run(): void;
}

function testAnimal(animal: Animal) {
    return (animal as Cat);
}
function testCat(cat: Cat) {
    return (cat as Animal);
}
```

- 双重断言
但是若使用双重断言，则可以打破「要使得 A 能够被断言为 B，只需要 A 兼容 B 或 B 兼容 A 即可」的限制，将任何一个类型断言为任何另一个类型。(最好不要用)
```javaScript
interface Icat {
  run(): void;
}
interface Fish {
  swim(): void;
}
function testCat(cat: Icat) {
  return (cat as any as Fish);
}
```

- 类型断言 vs 类型转换
类型断言不是类型转换，类型断言在编译后会被去掉
```javaScript
function toBoolean(something: any): boolean {
    return something as boolean;
}

toBoolean(1);
// 编译后转换如下
function toBoolean(something) {
  return something;
}

// 正确写法
function toBoolean(something: any): boolean {
  return Boolean(something)
}
```

- 类型断言 vs 类型声明
优先使用类型声明
```javaScript
const tom = getCacheData('tom') as Cat;
// 等价于
const tom: Cat = getCacheData('tom');
```

- 类型断言 vs 泛型
通过给 getCacheData 函数添加了一个泛型 <T>，我们可以更加规范的实现对 getCacheData 返回值的约束，这也同时去除掉了代码中的 any，是最优的一个解决方
```javaScript
function getCacheData<T>(key: string): T {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

const tom = getCacheData<Cat>('tom');
tom.run();
```

### 声明文件（待完整学习）

当使用第三方库的时，我们需要引用他的申明文件，才能获得对应的代码补全、接口提示等功能

- 新语索引

`declare var`  声明全局变量

`declare function`	声明全局方法

`declare class`	声明全局类

`declare enum`	声明全局枚举类型

`declare namespace`	声明（含有子属性的）全局对象

`interface、type`	声明全局类型

`export`	导出变量

`export namespace`	导出（含有子属性的）对象

`export default`	ES6默认导出

`export =`	commonjs导出模块

`export as namespace`	UMD库声明全局变量

`declare global`	扩展全局变量

`declare module`	扩展模块

- 什么事声明语句

假如我们想使用第三方库 jQuery，一种常见的方式是在 html 中通过 `<script>` 标签引入 jQuery，然后就可以使用全局变量 `$` 或 `jQuery` 了。

```typescript
// declare var并没有真的定义了一个变量，只是定义了全局变量jQuery的类型，仅仅用于编译时的检查，在编译结果中会被删除
declare var jQuery: (selector: string) => any;

jQuery('#foo');
```

- 什么是声明文件

通常我们会把声明语句放在一个单独的文件中（jQuery.d.ts）中，这就是声明文件

```typescript
// src/jQuery.d.ts
declare var jQuery: (selector: string) => any
```

```typescript
// src/index.ts
jQuery('#foo');
```

声明文件必须以.d.ts为后缀

一般来说，ts 会解析项目中所有的 `*.ts` 文件，当然也包含以 `.d.ts` 结尾的文件。所以当我们将 `jQuery.d.ts` 放到项目中时，其他所有 `*.ts` 文件就都可以获得 `jQuery` 的类型定义了。

```typescript
/path/to/project
├── src
|  ├── index.ts
|  └── jQuery.d.ts
└── tsconfig.json
```

假如仍然无法解析，那么可以检查下 `tsconfig.json` 中的 `files`、`include` 和 `exclude` 配置，确保其包含了 `jQuery.d.ts` 文件。

**第三方申明文件**

我们可以直接下载下来使用，但是更推荐的是使用 `@types` 统一管理第三方库的声明文件。

`@types` 的使用方式很简单，直接用 npm 安装对应的声明模块即可，以 jQuery 举例：

```typescript
npm install @types/jquery --save-dev
```

这是[搜索需要的声明文件](https://www.typescriptlang.org/dt/search?search=)

### TypeScript中类的用法

Typescript可以用三个修饰符，分别是public、private、protected；

`public` 	修饰的属性和方法是共有的，可以在任何地方被访问到，默认所有的属性和方法都是public

`private`	修饰的属性和方法是私有的，不能在声明属性类的外部访问

`protected`	修饰的属性和方法是被保护的，和`private` 类似，区别是它在子类也是允许被访问的

```typescript
class Animate {
  public name;
  public constructor(name) {
  	this.name = name;
  }
}

const a = new Animate('siri');
console.log(a.name)	// siri
a.name = 'others name'
a.name // others name;

```

很多时候，我们希望有些属性无法直接存取，这个时候就可以用private了

```typescript
class Animate {
  private name;
  public constructor(name) {
    this.name = name;
  }
}

const a = new Animate('siri');
console.log(a) // 报错，访问不了
```

使用 `private` 修饰的属性或方法，在子类中也是不允许访问的

```typescript
class Animate {
  private name = 'siri';
  public constructor(name) {
    this.name = name;
  }
}

class Cat extends Animate {
  public constructor(name) {
    super(name);
		console.log(this.name); // 报错，name是私有变量, 不能访问
  }
}
```

使用 `protected` 则可以在子类中访问

```typescript
class Aniamte {
	protected name = 'siri';
	public constructor(name) {
    this.name = this.name;
  }
}

class Cat extends Animate {
  public constructor(name) {
    super(name);
    console.log(this.name); // siri
  }
}
```

当构造函数修饰符为 `private` 时，该类允许被继承或实例化

```typescript
class Animate {
  public name;
  private constructor(name) {
    this.name = name;
  }
}

// 报错
class Cat extends Animate {
  public constructor(name) {
    super(name);
    console.log(name);
  }
}
  
const a = new Animate('siri'); // 报错
```

当构造函数被 `protected` 修饰时，该类只允许被继承，不能被实例化

```typescript
class Animate {
  public name = 'siri';
  protected constructor(name) {
    this.name = name;
  }
}

class Cat extends Animate {
  public constructor(name) {
    super(name);
    console.log(this.name); // siri
  }
}

const a = new Animate('siri') // 报错
```

- 修饰符和readonly还可以使用在构造函数参数中，等同于类中使用该属性同时给该属性赋值, 使代码更简洁

```typescript
class Animal {
  // public name: string;
  public constructor(public name) {
    // this.name = name;
  }
}
```

- readonly

只读属性关键词，是能用于属性声明，索引签名或者构造函数中

```typescript
class Animate {
  // 注意如果 readonly 和其他访问修饰符同时存在的话，需要写在其后面。
  public readonly name;
  public constructor(name) {
    this.name = name;
  }
}

const a = new Animate('siri');
console.log(a.name) // siri
a.name = 'others name'; // 报错
```

- 抽象类（保留着）

`abstract` 用于定义抽象类和其中的抽象方法。

抽象类是不能被实例化的

```typescript
abstract class Animate {
	public name;
  public constructor(name) {
    this.name = name;
  }
  public abstract getName()
}

// 报错
// const a = new Animate('siri');

class Cat extends Animate {
  public getName() {
		return name;
  }
}

const a new Cat('siri');

```

- 类的类型

```typescript
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  sayHi(): string {
    return `My name is ${this.name}`;
  }
}

let a: Animal = new Animal('Jack');
console.log(a.sayHi()); // My name is Jack
```



### 泛型

泛型：指在定义函数、接口和类的时候，不预先指定具体的类型；而在使用的时候再指定具体类型的一种特性
**简单的实现**

```javascript
function createArrays(length: number, value: any): Array<any> {
  const arrayFinal = [];
  for(let i = 0; i <= length; i += 1) {
    arrayFinal.push(value);
  }
  return arrayFinal;
}

createArrays(4, 0);
```
**通过泛型来更替**

```javascript
function createArrays<T>(length: number, value: T): Array<T> {
  const arrayFinal: T[] = [];
  for(let i = 0; i <= length; i += 1) {
    arrayFinal[i] = value;
  }
  return arrayFinal;
}

createArrays<number>(4, 0);
createArrays<string>(4, '1');
```

**多个参数类型**

```javascript
function swap<T, U>(arrays: [T, U]):[U, T] {
	return [arrays[1], arrays[0]];
}
swap([1, 2])
```

**泛型约束**

由于事先不知道它是那种类型，所以不能随意操作他的属性和方法

```typescript
function getPropsLength<T>(arg: T): T {
  // log中调用arg的length属性会导致报错
  // console.log(arg.length);
  return arg;
}
```

可以使用泛型约束的方式解决上述问题

```typescript
interface IpropsType {
  length: number;
}

function getPropsLength<T extends IpropsType>(arg: T): T{
  console.log(arg.length);
  return arg;
}

// 以下调用会报错
// getPropsLength(1212);
```

此时，如果调用时的参数没有length属性，那么编译时就会报错

多类型参数之间相互约束

```typescript
function copyFields<T extends U, U>(target: T, source: U): T {
    for (let id in source) {
        target[id] = (<T>source)[id];
    }
    return target;
}

let x = { a: 1, b: 2, c: 3, d: 4 };

copyFields(x, { b: 10, d: 20 });
```

上述中，我们使用两个类型的参数，其中 **T** 继承了 **U** ，这样就保证了 **U** 上不会出现 **T** 中不存在的字段

**泛型接口**

定义一个函数符合的形状

```typescript
interface IgetName {
  (name: string, age: number): boolean;
}
let getName: IgetName;
getName = function(name: string, age: number) {
  return name === 'slq' && age === 18;
}
```

泛型接口定义函数的形状

```typescript
interface IgetName {
  <T>(length: number, value: T): Array<T>;
}

let getName: IgetName;
getName = function<T>(length: number, value: T ): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}
getName(3, 'sdfdsd');
```

进一步写法

```typescript
interface IgetName<T> {
  (length: number, value: T): Array<T>;
}
let getName = IgetName<any>;
getName = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}
// 这里的T也可以省略
getName<T>(6, 1);
```

**泛型类**

```typescript
class testCls<T> {
  name: string;
  value: T;
  arrList: Array<T>;
  add: (key: T, value: T) => T
}

const OtestCls = new testCls<string>();
OtestCls.value = 17;
otestCls.arrList = [1, 2, 3];
otestCls.add = function(x, y) { return x + y };
```

**泛型参数的默认类型**

ts2.3版本后，我们可以在泛型代码参数中指定默认类型，当使用泛型时，代码中没有指定类型参数或者从实际参数中无法推断出时，这个默认类型就会起作用

```typescript
function testCls<T>(length: number, value: T): Array<T> {
  const arrFinal: T[] = [];
  for (let i = 0; i <= length; i += 1) {
    arrFinal[i] = value;
  }
  return arrFinal;
}
```
























