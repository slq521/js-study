// 原始数据类型
// 基础类型的可以赋予null和undefined值
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


// 任意值（尽量避免用任意值来表示变量的类型）
let changeName: any = 'slq';
changeName = 2345;
changeName = true;


// 类型推论
// 下面语句表示（let test: string = 'test';）
let testName = 'test';
// 如果定义时没有赋值，不管以后有没有赋值，都会被推断出为any；
let testAny;
testAny = 123;
testAny = true;


// 联合类型
// 用|来分割每个类型
let unionName: string|number;
unionName = 121212;
unionName = 'Union Name';

// 只能访问联合类型的所有类型共有的属性方法
function getNameLength(name: string|number):number {
  // 因为number没有length属性，所以ts检测会报错
  // return name.length;
  return ('' + name).length;
}

// 联合类型的变量在被赋值的时候
let someTypes: string|boolean|number = 'slq';
someTypes = 121;
// 接下来取someTypes的length属性会报错（上一行ts已经推断出为number类型）
// console.log(someTypes.length)


// 对象类型的接口(interface)
// 定义接口是建议加“I”前缀
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

// 只读属性(添加readonly前缀)
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


// 数组类型
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


// 函数的类型（一个函数有输入和输出，要在 TypeScript 中对其进行约束，需要把输入和输出都考虑到）
// 函数申明（函数申明、函数表达式）
// 函数(注意: 输入或多或少的参数是不被允许的)
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
