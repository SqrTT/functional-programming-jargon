Функциональное програмирование (ФП) предоставляет много приемуществ в результате чего его популярность растет. Однако, каждая парадигма програмиррования привносить свой уникальный жаргон и ФП не является исключением. Имея словарь, мы надеемся упростить изучение ФП.



Примеры представлены на JavaScript (ES2015). [Почему JavaScript?](https://github.com/hemanth/functional-programming-jargon/wiki/Why-JavaScript%3F)



В этом докуменете, где возможно, применяется терминология определенная в [Fantasy Land spec](https://github.com/fantasyland/fantasy-land)



__Содержание__
<!-- RM(noparent,notop) -->

* [Арность](#arity)
* [Функции высокого порядка (ФВП)](#higher-order-functions-hof)
* [Частичное применение](#partial-application)
* [Каррирование](#currying)
* [Авто каррирование](#auto-currying)
* [Композиция функций](#function-composition)
* [Чистота](#purity)
* [Побочные ефекты](#side-effects)
* [Идемпотентность](#idempotent)
* [Безточечный стиль](#point-free-style)
* [Предикат (утверждение)](#predicate)
* [Контракты](#contracts)
* [Защищенные функции](#guarded-functions)
* [Категории](#categories)
* [Значения](#value)
* [Константы](#constant)
* [Функтор](#functor)
* [Pointed Functor](#pointed-functor)
* [Подъем](#lift)
* [Ссылочная прозрачность](#referential-transparency)
* [Equational Reasoning](#equational-reasoning)
* [Лямбда](#lambda)
* [Лямбда исчисления](#lambda-calculus)
* [Ленивое выполнения](#lazy-evaluation)
* [Моноид](#monoid)
* [Монада](#monad)
* [Комонада](#comonad)
* [Апликативный функтор](#applicative-functor)
* [Морфизм](#morphism)
  * [Эндоморфизм](#endomorphism)
  * [Изоморфизм](#isomorphism)
* [Сетоид](#setoid)
* [Полугрупа](#semigroup)
* [Складываемый](#foldable)
* [Обходимый](#traversable)
* [Сихнатуры типов](#type-signatures)
* [Типы обединения](#union-type)
* [Тип продукта](#product-type)
* [Опция](#option)
* [Библиотеки для функционального программирования на JavaScript](#functional-programming-libraries-in-javascript)


<!-- /RM -->

## Арность {#arity}

Количество аргументов которые получает функция. Исходит из корня таких слов как унарный, бинарный, тернарный, итп. К примеру, функция получает два аргументы, поэтому она определана как бинарная фукнция или фукнция в которой арносто равна двум. Люди которые предпочитают гречиские корни латинским, иногда называют такие функции "диадическими". Аналогично, фукнции которые получают переменное количество аргументом называют "вариативными". В то время как банарная функция требует двух аргументов и только двух, вопреки каррированию и частичному применению (смотрите ниже). .

```javascript
const sum = (a, b) => a + b

const arity = sum.length
console.log(arity) // 2

// Арность функции sum равняется 2
```

## Функции высокого порядка (ФВП) {#higher-order-functions-hof}

Функции которые получают как аргумент функцию и/или возвращают функцию
.

```javascript
const filter = (predicate, xs) => {
  const result = []
  for (let idx = 0; idx < xs.length; idx++) {
    if (predicate(xs[idx])) {
      result.push(xs[idx])
    }
  }
  return result
}
```

```javascript
const is = (type) => (x) => Object(x) instanceof type
```

```javascript
filter(is(Number), [0, '1', 2, null]) // [0, 2]
```

## Частичное применение {#partial-application}

Частичное применение означает создание новой функции с преопределенными аргументами для оригинальной фукнции.



```javascript
// Вспомагательная фукнция которая создает частично примененые фукнции
// Получает фукнцию и аргументы
const partial = (f, ...args) =>
  // возвращает функцию которая получает остальные аргумены
  (...moreArgs) =>
    // и вызывает оригинальную функцию со всеми получеными аргументами
    f(...args, ...moreArgs)

// Чтонибудь для примера
const add3 = (a, b, c) => a + b + c

// Частичное приминения `2` и `3` к `add3` даст нам функцию с одним аргументом
const fivePlus = partial(add3, 2, 3) // (c) => 2 + 3 + c

fivePlus(4) // 9
```

Вы также можете испльзовать `Function.prototype.bind` для частичного применения функций в JS:

```javascript
const add1More = add3.bind(null, 2, 3) // (c) => 2 + 3 + c
```

Частичное применение помогает создать простые функции из более сложных заполняя их данными по мере получения. [Каррирование](#currying) функции автоматически частично применены.

## Каррирование {#currying}

Полцесс конвентирования фукнции которая получает несколько аргументов в функцию которая получает их по одному.

При каждом вызове функции она возвращает один аргумент и возвращает функцию которая получает один аргумент пока не будут получены все аргументы.


```javascript
const sum = (a, b) => a + b

const curriedSum = (a) => (b) => a + b

curriedSum(40)(2) // 42.

const add2 = curriedSum(2) // (b) => 2 + b

add2(10) // 12

```

## Авто каррирование {#auto-currying}

Трансформированние фукнции которая получает несколько аргументов в одну которая при получении меньше необходимого количества аргуменов возвращает фукнцию которая получает остальные. Когда функци получит необходимое количество аргументов то будет выполнена.

Lodash и ramda имеют функцию `curry` которая работает описаным путем.

```javascript
const add = (x, y) => x + y

const curriedAdd = _.curry(add)
curriedAdd(1, 2) // 3
curriedAdd(1) // (y) => 1 + y
curriedAdd(1)(2) // 3
```

__Почитать__
* [В пользу каррирования](http://fr.umio.us/favoring-curry/)
* [Ей Underscore, ты делаеш это неправильно!](https://www.youtube.com/watch?v=m3svKOdZijA)

## Композиция функций {#function-composition}

Действие соединения двух функций вместе для формирования третей, где результат первой функции является входным параметром для другой функции. 

```javascript
const compose = (f, g) => (a) => f(g(a)) // определение
const floorAndToString = compose((val) => val.toString(), Math.floor) // использование
floorAndToString(121.212121) // '121'
```

## Чистота {#purity}

Функция является чистой если возвращаемое значение определяется только входным значением, и не производит побочных ефектов.



```javascript
const greet = (name) => `Hi, ${name}`

greet('Brianne') // 'Hi, Brianne'
```

В отличии от следующих:

```javascript
window.name = 'Brianne'

const greet = () => `Hi, ${window.name}`

greet() // "Hi, Brianne"
```

Вывод, примера выше, основывается на данных сохраненных вне фукнции...

```javascript
let greeting

const greet = (name) => {
  greeting = `Hi, ${name}`
}

greet('Brianne')
greeting // "Hi, Brianne"
```

... а здесь модифицируется состояние вне функции.

## Побочные ефекты {#side-effects}

Говорят что функция или выражение имеет побочные еффекты если вместо возвращения значения она взаимодействует (читает или пишет в) внешнее зименяемое состояние. 

A function or expression is said to have a side effect if apart from returning a value, it interacts with (reads from or writes to) external mutable state.

```js
const differentEveryTime = new Date()
```

```js
console.log('Ввовд/вывод это побочный еффект!')
```

## Идемпотентность

Функция называется идемпотентной если при повторном приминеии резултатата ее работы к самой себе вырабатывает тот же результат.


```javascript
f(f(x)) ≍ f(x)
```

```javascript
Math.abs(Math.abs(10))
```

```javascript
sort(sort(sort([2, 1])))
```

## Безточечный стиль

Написание функций в которых определения явно не указывают на использование аргументов. Этот стиль обычто требует использования [каррирования](#currying) или других [функций высокого порядка](#higher-order-functions-hof). aka "молчаливое програмирование".

```javascript
// Имеем
const map = (fn) => (list) => list.map(fn)
const add = (a) => (b) => a + b

// Тогда

// не безточечный стиль - `numbers` явный аргумент
const incrementAll = (numbers) => map(add(1))(numbers)

// безточечный стиль - список неявный аргумент
const incrementAll2 = map(add(1))
```

`incrementAll` определяет и использует аргумент `numbers`, поэтому она не является безточечной.  `incrementAll2` написан простым комбинированием функций и значений, без упоминания их аргументов. Он __является__ безточечным.

Определения безточечных функций похожи на обычное присваивание без `function` или `=>`.

## Предикат (утверждение)

Предикат это функция которая возвращает истину или ложь для полученого значения. Распрастраненное использование это фукнция обратного вызова в фильтре коллекций.

```javascript
const predicate = (a) => a > 2

;[1, 2, 3, 4].filter(predicate) // [3, 4]
```

## Контракты

TODO

## Guarded Functions

TODO

## Категории

Объекты с привязанными фукркциями которые придерживаються определенных правил, например, [Моноид](#monoid)

## Значения

Все что может быть присвоено переменной.


```javascript
5
Object.freeze({name: 'John', age: 30}) // Фукнкия `freeze` принуждает к  неизменимости.
;(a) => a
;[1]
undefined
```

## Константы

Переменные которые не могут быть переприсвоены после определения.


```javascript
const five = 5
const john = {name: 'John', age: 30}
```
Константы имеют 
[ссылочную прозрачность](#referential-transparency). Это значит что они могут быдть заменены значениями которые они представлять без измененеия результата.

С вышеупомтянутыми константами выражение всегда будет возвращать `true`.

```javascript
john.age + five === ({name: 'John', age: 30}).age + (5)
```

## Functor

An object that implements a `map` function which, while running over each value in the object to produce a new object, adheres to two rules:

```js
// preserves identity
object.map(x => x) === object
```

and

```js
// composable
object.map(x => f(g(x))) === object.map(g).map(f)
```

(`f`, `g` be arbitrary functions)

A common functor in JavaScript is `Array` since it abides to the two functor rules:

```js
[1, 2, 3].map(x => x) // = [1, 2, 3]
```

and

```js
const f = x => x + 1
const g = x => x * 2

;[1, 2, 3].map(x => f(g(x))) // = [3, 5, 7]
;[1, 2, 3].map(g).map(f)     // = [3, 5, 7]
```

## Pointed Functor
An object with an `of` function that puts _any_ single value into it.

ES2015 adds `Array.of` making arrays a pointed functor.

```js
Array.of(1) // [1]
```

## Lift

Lifting is when you take a value and put it into an object like a [functor](#pointed-functor). If you lift a function into an [Applicative Functor](#applicative-functor) then you can make it work on values that are also in that functor.

Some implementations have a function called `lift`, or `liftA2` to make it easier to run functions on functors.

```js
const liftA2 = (f) => (a, b) => a.map(f).ap(b) // note it's `ap` and not `map`.

const mult = a => b => a * b

const liftedMult = liftA2(mult) // this function now works on functors like array

liftedMult([1, 2], [3]) // [3, 6]
liftA2((a, b) => a + b)([1, 2], [3, 4]) // [4, 5, 5, 6]
```

Lifting a one-argument function and applying it does the same thing as `map`.

```js
const increment = (x) => x + 1

lift(increment)([2]) // [3]
;[2].map(increment) // [3]
```


## Referential Transparency

An expression that can be replaced with its value without changing the
behavior of the program is said to be referentially transparent.

Say we have function greet:

```js
const greet = () => 'Hello World!'
```

Any invocation of `greet()` can be replaced with `Hello World!` hence greet is referentially transparent.

##  Equational Reasoning

When an application is composed of expressions and devoid of side effects, truths about the system can be derived from the parts.

## Lambda

An anonymous function that can be treated like a value.

```js
;(function (a) {
  return a + 1
})

;(a) => a + 1
```
Lambdas are often passed as arguments to Higher-Order functions.

```js
[1, 2].map((a) => a + 1) // [2, 3]
```

You can assign a lambda to a variable.

```js
const add1 = (a) => a + 1
```

## Lambda Calculus
A branch of mathematics that uses functions to create a [universal model of computation](https://en.wikipedia.org/wiki/Lambda_calculus).

## Lazy evaluation

Lazy evaluation is a call-by-need evaluation mechanism that delays the evaluation of an expression until its value is needed. In functional languages, this allows for structures like infinite lists, which would not normally be available in an imperative language where the sequencing of commands is significant.

```js
const rand = function*() {
  while (1 < 2) {
    yield Math.random()
  }
}
```

```js
const randIter = rand()
randIter.next() // Each execution gives a random value, expression is evaluated on need.
```

## Monoid

An object with a function that "combines" that object with another of the same type.

One simple monoid is the addition of numbers:

```js
1 + 1 // 2
```
In this case number is the object and `+` is the function.

An "identity" value must also exist that when combined with a value doesn't change it.

The identity value for addition is `0`.
```js
1 + 0 // 1
```

It's also required that the grouping of operations will not affect the result (associativity):

```js
1 + (2 + 3) === (1 + 2) + 3 // true
```

Array concatenation also forms a monoid:

```js
;[1, 2].concat([3, 4]) // [1, 2, 3, 4]
```

The identity value is empty array `[]`

```js
;[1, 2].concat([]) // [1, 2]
```

If identity and compose functions are provided, functions themselves form a monoid:

```js
const identity = (a) => a
const compose = (f, g) => (x) => f(g(x))
```
`foo` is any function that takes one argument.
```
compose(foo, identity) ≍ compose(identity, foo) ≍ foo
```

## Monad

A monad is an object with [`of`](#pointed-functor) and `chain` functions. `chain` is like [`map`](#functor) except it un-nests the resulting nested object.

```js
// Implementation
Array.prototype.chain = function (f) {
  return this.reduce((acc, it) => acc.concat(f(it)), [])
}

// Usage
;Array.of('cat,dog', 'fish,bird').chain((a) => a.split(',')) // ['cat', 'dog', 'fish', 'bird']

// Contrast to map
;Array.of('cat,dog', 'fish,bird').map((a) => a.split(',')) // [['cat', 'dog'], ['fish', 'bird']]
```

`of` is also known as `return` in other functional languages.
`chain` is also known as `flatmap` and `bind` in other languages.

## Comonad

An object that has `extract` and `extend` functions.

```js
const CoIdentity = (v) => ({
  val: v,
  extract () {
    return this.val
  },
  extend (f) {
    return CoIdentity(f(this))
  }
})
```

Extract takes a value out of a functor.

```js
CoIdentity(1).extract() // 1
```

Extend runs a function on the comonad. The function should return the same type as the comonad.

```js
CoIdentity(1).extend((co) => co.extract() + 1) // CoIdentity(2)
```

## Applicative Functor

An applicative functor is an object with an `ap` function. `ap` applies a function in the object to a value in another object of the same type.

```js
// Implementation
Array.prototype.ap = function (xs) {
  return this.reduce((acc, f) => acc.concat(xs.map(f)), [])
}

// Example usage
;[(a) => a + 1].ap([1]) // [2]
```

This is useful if you have two objects and you want to apply a binary function to their contents.

```js
// Arrays that you want to combine
const arg1 = [1, 3]
const arg2 = [4, 5]

// combining function - must be curried for this to work
const add = (x) => (y) => x + y

const partiallyAppliedAdds = [add].ap(arg1) // [(y) => 1 + y, (y) => 3 + y]
```

This gives you an array of functions that you can call `ap` on to get the result:

```js
partiallyAppliedAdds.ap(arg2) // [5, 6, 7, 8]
```

## Morphism

A transformation function.

### Endomorphism

A function where the input type is the same as the output.

```js
// uppercase :: String -> String
const uppercase = (str) => str.toUpperCase()

// decrement :: Number -> Number
const decrement = (x) => x - 1
```

### Isomorphism

A pair of transformations between 2 types of objects that is structural in nature and no data is lost.

For example, 2D coordinates could be stored as an array `[2,3]` or object `{x: 2, y: 3}`.

```js
// Providing functions to convert in both directions makes them isomorphic.
const pairToCoords = (pair) => ({x: pair[0], y: pair[1]})

const coordsToPair = (coords) => [coords.x, coords.y]

coordsToPair(pairToCoords([1, 2])) // [1, 2]

pairToCoords(coordsToPair({x: 1, y: 2})) // {x: 1, y: 2}
```



## Setoid

An object that has an `equals` function which can be used to compare other objects of the same type.

Make array a setoid:

```js
Array.prototype.equals = (arr) => {
  const len = this.length
  if (len !== arr.length) {
    return false
  }
  for (let i = 0; i < len; i++) {
    if (this[i] !== arr[i]) {
      return false
    }
  }
  return true
}

;[1, 2].equals([1, 2]) // true
;[1, 2].equals([0]) // false
```

## Semigroup

An object that has a `concat` function that combines it with another object of the same type.

```js
;[1].concat([2]) // [1, 2]
```

## Foldable

An object that has a `reduce` function that can transform that object into some other type.

```js
const sum = (list) => list.reduce((acc, val) => acc + val, 0)
sum([1, 2, 3]) // 6
```

## Traversable

TODO

## Type Signatures

Often functions in JavaScript will include comments that indicate the types of their arguments and return values.

There's quite a bit of variance across the community but they often follow the following patterns:

```js
// functionName :: firstArgType -> secondArgType -> returnType

// add :: Number -> Number -> Number
const add = (x) => (y) => x + y

// increment :: Number -> Number
const increment = (x) => x + 1
```

If a function accepts another function as an argument it is wrapped in parentheses.

```js
// call :: (a -> b) -> a -> b
const call = (f) => (x) => f(x)
```

The letters `a`, `b`, `c`, `d` are used to signify that the argument can be of any type. The following version of `map` takes a function that transforms a value of some type `a` into another type `b`, an array of values of type `a`, and returns an array of values of type `b`.

```js
// map :: (a -> b) -> [a] -> [b]
const map = (f) => (list) => list.map(f)
```

__Further reading__
* [Ramda's type signatures](https://github.com/ramda/ramda/wiki/Type-Signatures)
* [Mostly Adequate Guide](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch7.html#whats-your-type)
* [What is Hindley-Milner?](http://stackoverflow.com/a/399392/22425) on Stack Overflow

## Union type
A union type is the combination of two types together into another one.

JS doesn't have static types but let's say we invent a type `NumOrString` which is a sum of `String` and `Number`.

The `+` operator in JS works on strings and numbers so we can use this new type to describe its inputs and outputs:

```js
// add :: (NumOrString, NumOrString) -> NumOrString
const add = (a, b) => a + b

add(1, 2) // Returns number 3
add('Foo', 2) // Returns string "Foo2"
add('Foo', 'Bar') // Returns string "FooBar"
```

Union types are also known as algebraic types, tagged unions, or sum types.

There's a [couple](https://github.com/paldepind/union-type) [libraries](https://github.com/puffnfresh/daggy) in JS which help with defining and using union types.

## Product type

A **product** type combines types together in a way you're probably more familiar with:

```js
// point :: (Number, Number) -> {x: Number, y: Number}
const point = (x, y) => ({x: x, y: y})
```
It's called a product because the total possible values of the data structure is the product of the different values.

See also [Set theory](https://en.wikipedia.org/wiki/Set_theory).

## Option
Option is a [union type](#union-type) with two cases often called `Some` and `None`.

Option is useful for composing functions that might not return a value.

```js
// Naive definition

const Some = (v) => ({
  val: v,
  map (f) {
    return Some(f(this.val))
  },
  chain (f) {
    return f(this.val)
  }
})

const None = () => ({
  map (f) {
    return this
  },
  chain (f) {
    return this
  }
})

// maybeProp :: (String, {a}) -> Option a
const maybeProp = (key, obj) => typeof obj[key] === 'undefined' ? None() : Some(obj[key])
```
Use `chain` to sequence functions that return `Option`s
```js

// getItem :: Cart -> Option CartItem
const getItem = (cart) => maybeProp('item', cart)

// getPrice :: Item -> Option Number
const getPrice = (item) => maybeProp('price', item)

// getNestedPrice :: cart -> Option a
const getNestedPrice = (cart) => getItem(obj).chain(getPrice)

getNestedPrice({}) // None()
getNestedPrice({item: {foo: 1}}) // None()
getNestedPrice({item: {price: 9.99}}) // Some(9.99)
```

`Option` is also known as `Maybe`. `Some` is sometimes called `Just`. `None` is sometimes called `Nothing`.

## Библиотеки для функционального программирования на JavaScript

* [Ramda](https://github.com/ramda/ramda)
* [Folktale](http://folktalejs.org)
* [lodash](https://github.com/lodash/lodash)
* [Underscore.js](https://github.com/jashkenas/underscore)
* [Lazy.js](https://github.com/dtao/lazy.js)
* [maryamyriameliamurphies.js](https://github.com/sjsyrek/maryamyriameliamurphies.js)
* [Haskell in ES6](https://github.com/casualjavascript/haskell-in-es6)

---

__P.S:__ This repo is successful due to the wonderful [contributions](https://github.com/hemanth/functional-programming-jargon/graphs/contributors)!
