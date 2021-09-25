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
  // 因为number没有length属性，所有ts检测会报错
  // return name.length;
  return ('' + name).length;
}

// 联合类型的变量在被赋值的时候
let someTypes: string|boolean|number = 'slq';
someTypes = 121;
// 接下来取someTypes的length属性会报错（上一行ts已经推断出为number类型）
// console.log(someTypes.length)
