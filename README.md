# ECMAScript 6 <sup>[git.io/es6features](http://git.io/es6features)</sup>

## 導入
ECMAScript6（ES6）はECMAScriptスタンダードの次期バージョンであり、2014年10月の承認を目指しています。ES6によって多くのアップデートがJavaScript言語にもたらさます。このような大きな変更は、ES5が2009年に標準になって以来のことです。主要なJavaScriptエンジンにおけるES6の実装状況は[underway now](http://kangax.github.io/es5-compat-table/es6/)を参照してください。

ECMAScript6の詳細仕様は[draft ES6 standard](https://people.mozilla.org/~jorendorff/es6-draft.html)で公開されています。

ECMAScript6には次のような新機能が含まれています。
- [Arrows](#arrows)
- [Classes](#classes)
- [Enhanced Object Literals](#enhanced-object-literals)
- [Template Strings](#template-strings)
- [Destructuring](#destructuring)
- [Default + Rest + Spread](#default--rest--spread)
- [Let + Const](#let--const)
- [Iterators + For..of](#iterators--forof)
- [Generators](#generators)
- [Comprehensions](#comprehensions)
- [Unicode](#unicode)
- [Modules](#modules)
- [Module Loaders](#module-loaders)
- [Map + Set + Weakmap + Weakset](#map--set--weakmap--weakset)
- [Proxies](#proxies)
- [Symbols](#symbols)
- [Subclassable Built-ins](#subclassable-built-ins)
- [Math + Number + String + Object APIs](#math--number--string--object-apis)
- [Binary and Octal Literals](#binary-and-octal-literals)
- [Promises](#promises)
- [Reflect API](#reflect-api)
- [Tail Calls](#tail-calls)

## ECMAScript6の新機能

### Arrows
アロー関数（Arrow Function）は関数の省略記法で`=>`を用いて記述します。これはC#、Java8、CoffeeScriptの機能と似たシンタックスです。アロー関数は1行の式（Expression）としても利用できますし、波括弧`{}`を用いて複数行のステートメント（Statement）としても利用することができます。また、アロー関数内での`this`は通常の関数とは異なります（詳細は以下の例を参照）。

```JavaScript
// 1行で用いる場合
var odds = evens.map(v => v + 1);
var nums = evens.map((v, i) => v + i);

// 複数行で用いる場合
nums.forEach(v => {
  if (v % 5 === 0)
    fives.push(v);
});

// thisの参照先
function Person () {
  this.age = 0;
  setInterval(function () {
    //「this = window」になるので意図しない動きになる
    console.log(this.age++);
  }, 1000);
}

function Person () {
  this.age = 0;
  setInterval(() => {
    // thisはPersonを指すため、意図した動きになる
    console.log(this.age++);
  }, 1000);
}
```

### Classes
クラス（Class）はプロトタイプをベースとしていて、オブジェクト指向パターンの上に成り立っています。宣言的な型を独自に定義することでクラスパターンをより利用しやすくなり、また相互運用可能性を促進します。Classはプロトタイプベースの継承をサポートしており、親（super）クラスの呼び出しや、インスタンスメソッド・スタティックメソッドやコンストラクタなどの機能を提供します。

```JavaScript
class Animal {
  constructor() {
    this.id = 'id-' + new Date().getTime();
  }
  walk () {
    console.log('an animal is walking...');
  }
  static getInstance () {
    return new Animal();
  }
}

class Dog extends Animal {
  constructor(name) {
    super();
    this.name = name;
  }
  walk () {
    console.log(this.name + ' is walking...');
  }
}
```

### Enhanced Object Literals
オブジェクトリテラルの記述が拡張され、プロトタイプの指定、`foo: foo`の省略記法、メソッドの定義、superの呼び出しを行うことができるようになりました。これによりオブジェクトリテラルとクラス定義をより近しいものとすることができ、同じ機能を持つ異なるものにおいて、オブジェクトベースデザインの利点を享受することができます。

```JavaScript
function createObject (name) {
  return {
    // プロトタイプの指定
    __proto__: theProtoObj,
    // プロパティの省略定義
    name,
    // メソッド定義
    toString() {
     // Super呼び出し
     // TODO super.xxx in a object literal doesn't work.
     // return "d " + super.toString();
    },
    // ダイナミックなプロパティ名
    [ 'prop_' + (() => 42)() ]: 42
  }
}
var obj = createObject('foo');
console.log(obj.name); // foo
```

### Template Strings
テンプレート文字列（Template Strings）は文字列の構築に役立ちます。これはPerlやPythonなどの文字列挿入の形式に似ています。カスタマイズのために任意でタグを文字列の構築に追加することができるため、インジェクション攻撃（injection attack）を防げたり、文字列の組み立てをより簡単に行うことができます。

```JavaScript
// 最も基本的な文字列の指定
`In JavaScript '\n' is a line-feed.`

// 複数行
`In JavaScript this is
 not legal.`

// DOM queryの構築
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`

// HTTPリクエストを構築する例
GET`http://foo.org/bar?a=${a}&b=${b}
    Content-Type: application/json
    X-Credentials: ${credentials}
    { "foo": ${foo},
      "bar": ${bar}}`(myOnReadyStateChangeHandler);
```

### Destructuring
Destructuring allows binding using pattern matching, with support for matching arrays and objects.  Destructuring is fail-soft, similar to standard object lookup `foo["bar"]`, producing `undefined` values when not found.

### Destructuring
デストラクチャー（Destructuring）はパターンマッチングを用いて、配列やオブジェクトに値を設定することができます。デストラクチャーはフェールソフト（fail-soft）な実装であり、例えば`foo["bar"]`で対象を探し、もし値が見つからない場合には`undefined`が設定されます。

```JavaScript
// 配列への代入
var [a, , b] = [1,2,3];

// オブジェクトへの代入
var { op: a, lhs: { op: b }, rhs: c }
       = getASTNode()

// オブジェクトへの代入（省略記法）
var {op, lhs, rhs} = getASTNode()

// パラメータにも使うことができます
function g({name: x}) {
  console.log(x);
}
g({name: 5})

// フェールソフトの例
var [a] = [];
a === undefined;
```

### Default + Rest + Spread
Callee-evaluated default parameter values.  Turn an array into consecutive arguments in a function call.  Bind trailing parameters to an array.  Rest replaces the need for `arguments` and addresses common cases more directly.

### Default + Rest + Spread
関数の引数に初期値を設定できます。関数呼び出しで複数のパラメータを指定する場合に、配列で指定することができます。可変引数が利用可能になりました。Rest replaces the need for `arguments` and addresses common cases more directly.

```JavaScript
function f(x, y=12) {
  // 第2引数が指定されない場合には、y=12となります
  return x + y;
}
f(3) == 15
```
```JavaScript
function f(x, ...y) {
  // yは配列です
  return x * y.length;
}
f(3, "hello", true) == 6
```
```JavaScript
function f(x, y, z) {
  return x + y + z;
}
// 関数の引数を配列形式で指定しています
f(...[1,2,3]) == 6
```

### Let + Const
Block-scoped binding constructs.  `let` is the new `var`.  `const` is single-assignment.  Static restrictions prevent use before assignment.


### Let + Const
変数宣言の新しい方法です。`let`は`var`の新しい形式で、`const`は一度だけ値を代入することができます。静的な制約を用いることで値代入前に変数を使うことを防ぐことができます。

```JavaScript
function f() {
  {
    let x;
    {
      // OK: 異なるブロックスコープ内の変数
      const x = "sneaky";
      // NG: 2度目の代入はダメ
      x = "foo";
    }
    // NG: 既に同一ブロック内で定義済み
    let x = "inner";
  }
}
```

### Iterators + For..Of
Iterator objects enable custom iteration like CLR IEnumerable or Java Iteratable.  Generalize `for..in` to custom iterator-based iteration with `for..of`.  Don’t require realizing an array, enabling lazy design patterns like LINQ.

### Iterators + For..Of
イテレーターオブジェクト（Iterator Object）はCLRのIEnumberableやJavaのIterableのような、イテレーションを提供します。`for..in`をイテレーションベースにしたものが`for..of`です。配列か否かを気にする必要はなく、LINQなどの遅延デザインパターン（Lazy Design Pattern）を利用することができます。

```JavaScript
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0, cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur }
      }
    }
  }
}

for (var n of fibonacci) {
  // 1000で終わり
  if (n > 1000)
    break;
  print(n);
}
```

Iteration is based on these duck-typed interfaces (using [TypeScript](http://typescriptlang.org) type syntax for exposition only):

Iterationは次のようなダックタイピングなインターフェースを元にしています（using [TypeScript](http://typescriptlang.org) type syntax for exposition only）。

```TypeScript
interface IteratorResult {
  done: boolean;
  value: any;
}
interface Iterator {
  next(): IteratorResult;
}
interface Iterable {
  [Symbol.iterator](): Iterator
}
```

### Generators
Generators simplify iterator-authoring using `function*` and `yield`.  A function declared as function* returns a Generator instance.  Generators are subtypes of iterators which include additional  `next` and `throw`.  These enable values to flow back into the generator, so `yield` is an expression form which returns a value (or throws).

### Generators
ジェネレーター（Generator）は`function*`と`yield`を用いて、イテレーターの扱いをシンプルにします。`function*`で宣言された関数はジェネレーターのインスタンスを返却します。ジェネレーターはイテレーターのサブタイプであり、`next`と`throw`の機能を追加で保持しています。これらを用いることで値を一連で扱うことができます。`yeild`は返却される値（またはthrowされる値）を表します。

Note: Can also be used to enable ‘await’-like async programming, see also ES7 `await` proposal.

注意：`await`のような非同期プログラミングも利用することができます。詳しくはES7の`await`の提案を参照のこと。

```JavaScript
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  print(n);
}
```

The generator interface is (using [TypeScript](http://typescriptlang.org) type syntax for exposition only):

ジェネレーターのインターフェースは次の通りです（using [TypeScript](http://typescriptlang.org) type syntax for exposition only）。

```TypeScript
interface Generator extends Iterator {
    next(value?: any): IteratorResult;
    throw(exception: any);
}
```

### Comprehensions
Array and generator comprehensions provide simple declarative list processing similar as used in many functional programming patterns.

### Comprehensions
配列やジェネレータなどのデータ構造はシンプルな宣言的なリスト機能を提供し、それらは多くの関数型プログラミングのパターンにおいて似たように利用されます。

```JavaScript
// 配列
var results = [
  for(c of customers)
    if (c.city == "Seattle")
      { name: c.name, age: c.age }
]

// ジェネレーター
var results = (
  for(c of customers)
    if (c.city == "Seattle")
      { name: c.name, age: c.age }
)
```

### Unicode
Non-breaking additions to support full Unicode, including new unicode literal form in strings and new RegExp `u` mode to handle code points, as well as new APIs to process strings at the 21bit code points level.  These additions support building global apps in JavaScript.

### Unicode
Unicodeがサポートされ、その中には新しいUnicodeの文字が含まれます。正規表現（RegExp）で`u`モードを用いることで扱うことができ、それは21ビットコードポイントレベルの処理を行う新しいAPIと同様です。このサポートによりJavaScriptをグローバルなアプリケーションに利用する手助けとなります。

```JavaScript
// ES5.1と同じ
"𠮷".length == 2

// 新しい世紀表現の利用方法（‘u’の利用）
"𠮷".match(/./u)[0].length == 2

// 新しい定義方法
"\u{20BB7}"=="𠮷"=="\uD842\uDFB7"

// コードポイント
"𠮷".codePointAt(0) == 0x20BB7

// コードポイントによるfor..ofの利用
for(var c of "𠮷") {
  console.log(c);
}
```

### Modules
Language-level support for modules for component definition.  Codifies patterns from popular JavaScript module loaders (AMD, CommonJS). Runtime behaviour defined by a host-defined default loader.  Implicitly async model – no code executes until requested modules are available and processed.

### Modules
言語レベルでコンポーネント定義を行うことのできるmoduleがサポートされます。一般的なJavaScriptモジュールローダー（AMDやCommonJSなど）により体系化されたパターンを利用します。ランタイムの振る舞いは、ホストが定義しているデフォルトローダーによって定義されます。これは暗黙的な非同期モデルで、モジュールとして呼び出されたコードはそれが呼び出されるまで、実行されません。

```JavaScript
// lib/math.js
export function sum(x, y) {
  return x + y;
}
export var pi = 3.141593;
```
```JavaScript
// app.js
module math from "lib/math";
alert("2π = " + math.sum(math.pi, math.pi));
```
```JavaScript
// otherApp.js
import {sum, pi} from "lib/math";
alert("2π = " + sum(pi, pi));
```

Some additional features include `export default` and `export *`:

`export default`や`export *`などの機能もあります。

```JavaScript
// lib/mathplusplus.js
export * from "lib/math";
export var e = 2.71828182846;
export default function(x) {
    return Math.exp(x);
}
```
```JavaScript
// app.js
module math from "lib/mathplusplus";
import exp from "lib/mathplusplus";
alert("2π = " + exp(math.pi, math.e));
```

### Module Loaders
Module loaders support:
- Dynamic loading
- State isolation
- Global namespace isolation
- Compilation hooks
- Nested virtualization

### Module Loaders
モジュールローダーサポート:
- ダイナミックローディング（Dynamic Loading）
- 状態の独立（State Isolation）
- グローバル名前空間の独立（Global Namespace Isolation）
- コンパイルフック（Compilation Hooks）
- ネスト化された仮想化（Nested Virtualization）

The default module loader can be configured, and new loaders can be constructed to evaluated and load code in isolated or constrained contexts.

デフォルトモジュールローダーを設定することが可能です。新しいローダーによって独立したコード（またはコンテキストを指定したコード）を構築することができます。

```JavaScript
// ダイナミックロードディング – ‘System’はデフォルトローダー
System.import('lib/math').then(function(m) {
  alert("2π = " + m.sum(m.pi, m.pi));
});

// Create execution sandboxes – new Loaders
var loader = new Loader({
  global: fixup(window) // replace ‘console.log’
});
loader.eval("console.log('hello world!');");

// Directly manipulate module cache
System.get('jquery');
System.set('jquery', Module({$: $})); // WARNING: not yet finalized
```

### Map + Set + WeakMap + WeakSet
Efficient data structures for common algorithms.  WeakMaps provides leak-free object-key’d side tables.

### Map + Set + WeakMap + WeakSet
多くのアルゴリズムで有益なデータ構造がサポートされます。WeakMapはメモリリークフリーなKey-Value構造を提供します。

```JavaScript
// Set
var s = new Set();
s.add("hello").add("goodbye").add("hello");
s.size === 2;
s.has("hello") === true;

// Map
var m = new Map();
m.set("hello", 42);
m.set(s, 34);
m.get(s) == 34;

// Weak Map
var wm = new WeakMap();
wm.set(s, { extra: 42 });
wm.size === undefined

// Weak Set
var ws = new WeakSet();
ws.add({ data: 42 });
// Because the added object has no other references, it will not be held in the set
```

### Proxies
Proxies enable creation of objects with the full range of behaviors available to host objects.  Can be used for interception, object virtualization, logging/profiling, etc.

### Proxies
プロキシ（Proxy）を用いることで、ホストオブジェクト（host object）に対して柔軟な振る舞いを設定することができます。処理の割り込みをしたり、オブジェクトの仮想化を行ったり、ロギングやプロファイリングを行うことができます。

```JavaScript
// Proxying a normal object
var target = {};
var handler = {
  get: function (receiver, name) {
    return `Hello, ${name}!`;
  }
};

var p = new Proxy(target, handler);
p.world === 'Hello, world!';
```

```JavaScript
// Proxying a function object
var target = function () { return 'I am the target'; };
var handler = {
  apply: function (receiver, ...args) {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);
p() === 'I am the proxy';
```

There are traps available for all of the runtime-level meta-operations:

```JavaScript
var handler =
{
  get:...,
  set:...,
  has:...,
  deleteProperty:...,
  apply:...,
  construct:...,
  getOwnPropertyDescriptor:...,
  defineProperty:...,
  getPrototypeOf:...,
  setPrototypeOf:...,
  enumerate:...,
  ownKeys:...,
  preventExtensions:...,
  isExtensible:...
}
```

### Symbols
Symbols enable access control for object state.  Symbols allow properties to be keyed by either `string` (as in ES5) or `symbol`.  Symbols are a new primitive type. Optional `name` parameter used in debugging - but is not part of identity.  Symbols are unique (like gensym), but not private since they are exposed via reflection features like `Object.getOwnPropertySymbols`.

### Symbols
シンボル（Symbol）はオブジェクトの状態コントロールにアクセスすることを可能にします。シンボルのプロパティには`string（ES5）でも`symbol`でもどちらも取ることができます。シンボルは新しいプリミティブ型です。オプションで`name`プロパティを設定することができデバッグに利用できます（が一意である保証はありません）。シンボルは（gensymのように）固有のものですが、`Object.getOwnPropertySymbols`などの機能を通してシンボルは公開されるので、プライベートではありません。


```JavaScript
(function() {

  // module scoped symbol
  var key = Symbol("key");

  function MyClass(privateData) {
    this[key] = privateData;
  }

  MyClass.prototype = {
    doStuff: function() {
      ... this[key] ...
    }
  };

})();

var c = new MyClass("hello")
c["key"] === undefined
```

### Subclassable Built-ins
In ES6, built-ins like `Array`, `Date` and DOM `Element`s can be subclassed.

### Subclassable Built-ins
ES6では`Array`、`Date`、DOM `Element`といった組み込みクラスのサブクラスを作成することができます。

Object construction for a function named `Ctor` now uses two-phases (both virtually dispatched):
- Call `Ctor[@@create]` to allocate the object, installing any special behavior
- Invoke constructor on new instance to initialize

`Ctor`という名前の関数を使ってオブジェクトを生成します。現在は以下２つのステップを行います（以下どちらも仮想的にはディスパッティされています）
- `Ctor[@@create]`を呼び出しオブジェクトの割り当てを行い、独自の振る舞いを定義します。
- コンストラクターを呼び出し、新しいインスタンスを生成します。

The known `@@create` symbol is available via `Symbol.create`.  Built-ins now expose their `@@create` explicitly.

`@@create`シンボルは、`Symbol.create`を用いて利用することができます。ビルドインクラスは`@@create`を公開しています。

```JavaScript
// Arrayクラス（擬似的に再現しています）
class Array {
    constructor(...args) { /* ... */ }
    static [Symbol.create]() {
        // Install special [[DefineOwnProperty]]
        // to magically update 'length'
    }
}

// Arrayクラスの独自サブクラス
class MyArray extends Array {
    constructor(...args) { super(...args); }
}

// Two-phase 'new':
// 1) Call @@create to allocate object
// 2) Invoke constructor on new instance
var arr = new MyArray();
arr[1] = 12;
arr.length == 2
```

### Math + Number + String + Object APIs
Many new library additions, including core Math libraries, Array conversion helpers, and Object.assign for copying.

### Math + Number + String + Object APIs
Mathライブラリ、Arrayを扱うヘルパー、などの様々な機能が追加され、またコピーのためのObject.assignも追加されました。

```JavaScript
Number.EPSILON
Number.isInteger(Infinity) // false
Number.isNaN("NaN") // false

Math.acosh(3) // 1.762747174039086
Math.hypot(3, 4) // 5
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2) // 2

"abcde".contains("cd") // true
"abc".repeat(3) // "abcabcabc"

Array.from(document.querySelectorAll('*')) // Returns a real Array
Array.of(1, 2, 3) // Similar to new Array(...), but without special one-arg behavior
[0, 0, 0].fill(7, 1) // [0,7,7]
[1,2,3].findIndex(x => x == 2) // 1
["a", "b", "c"].entries() // iterator [0, "a"], [1,"b"], [2,"c"]
["a", "b", "c"].keys() // iterator 0, 1, 2
["a", "b", "c"].values() // iterator "a", "b", "c"

Object.assign(Point, { origin: new Point(0,0) })
```

### Binary and Octal Literals
Two new numeric literal forms are addded for binary (`b`) and octal (`o`).

### Binary and Octal Literals
数値の表現に、`b`を用いたバイナリー記述と`o`を用いた8進数の記述が新たに加わりました。

```JavaScript
0b111110111 === 503 // true
0o767 === 503 // true
```

### Promises
Promises are a library for asynchronous programming.  Promises are a first class representation of a value that may be made available in the future.  Promises are used in many existing JavaScript libraries.

### Promises
Promiseは非同期プログラミングを行うためのライブラリです。Promiseを用いることで将来利用可能となる値を表現することができます。Promiseは既に多くのJavaScriptライブラリで利用されています。

```JavaScript
function timeout(duration = 0) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, duration);
    })
}

var p = timeout(1000).then(() => {
    return timeout(2000);
}).then(() => {
    throw new Error("hmm");
}).catch(err => {
    return Promise.all([timeout(100), timeout(200)]);
})
```

### Reflect API
Full reflection API exposing the runtime-level meta-operations on objects.  This is effectively the inverse of the Proxy API, and allows making calls corresponding to the same meta-operations as the proxy traps.  Especially useful for implementing proxies.

### Reflect API
リフレクションAPI（Reflection API）は実行時にオブジェクトのメタ操作を行うことができます。これはProxy APIとは逆のことを提供するものであり、Proxyトラップと同じようなメタ操作に対応する呼び出しを行うことができます。特にProxyを実装する際に有効です。

```JavaScript
// No sample yet
```

### Tail Calls
Calls in tail-position are guaranteed to not grow the stack unboundedly.  Makes recursive algorithms safe in the face of unbounded inputs.

### Tail Calls
テールコール（Tail Call）は上限なきスタックの増加を防ぐことができます。制御しきれないような大きな入力を受け付けた場合にも、再帰呼び出しアルゴリズムを正常に動作させることができます。


```JavaScript
function factorial(n, acc = 1) {
    'use strict';
    if (n <= 1) return acc;
    return factorial(n - 1, n * acc);
}

// 現在のほとんどの実装系ではスタックオーバーフロー（Stack Overflow）が発生します
// しかし、ES6ではそれが発生しません
factorial(100000)
```
