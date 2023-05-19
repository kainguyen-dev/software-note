# Typescript

Because JS is loosely typed, typescript adding type checking system to javascript.

```typescript
let var1: string = "Hello";
var1 = 10;
console.log(var1);
```

Interface support:

```typescript
interface Point {
  x: number;
  y: number;
}
```

Interface vs Type:

	- Interface is extendable, while type is not.

Type literal:

```typescript
function printText(s: string, alignment: "left" | "right" | "center") {
  // ...
}
printText("Hello, world", "left");
printText("G'day, mate", "centre");
``` 

Generic:

```typescript
// function that returns a tuple
let getTuple: <T, U>( a: T, b: U ) => [ T, U ] = ( a, b ) => {
    return [ a, b ];
}

let stringArray = getTuple( 'hello', 'world' );

let numberArray = getTuple( 1.25, 2.56 );

//case error
let mixedArray = getTuple( 1.25, 'world' );

// Property 'toUpperCase' does not exist on type 'NS'.
console.log( stringArray.map( s => s.toUpperCase() ) );

// Error: Property 'toFixed' does not exist on type 'NS'.
console.log( numberArray.map( n => n.toFixed() ) );
```

## 1. Define type

- Define type can be use by interface

```typescript

interface User {
    name: string,
    id: number
}

const duy: User = {
    name: 'Duy',
    id: 12
}

// Interface can be used as type of instance

class UserAccount {
    name: string;
    id: number;

    constructor(name: string, id: number) {
        this.name = name;
        this.id = id;
    }
}

const a: User = new UserAccount('A', 1000);

```

- Primitive data type:
	- number
	- string
	- boolean
	- bigint
	- symbol
	- any

#### Composing Types:

- We can use union to create complex type or set of value allow to return

```typescript
type WindowStates = "open" | "closed" | "minimized";
type LockStates = "locked" | "unlocked";
type PositiveOddNumbersUnderTen = 1 | 3 | 5 | 7 | 9;
```

- Generic type:

```typescript

interface BackPack<T> {
    add: (obj:T) => void;
    get: (idx: number) => T;
}

const backpack: Backpack<string>;

const object = backpack.get();

backpack.add("item1");

```

#### Structural type system (duck typing)

- In a structural type system, if two objects have the same shape, they are considered to be of the same type.

```typescript
interface Point {
  x: number;
  y: number;
}
 
function logPoint(p: Point) {
  console.log(`${p.x}, ${p.y}`);
}
 
// logs "12, 26"
const point = { x: 12, y: 26 };
logPoint(point);

```
- There is no difference between how class and interface conform to shapes.
- Difference **duck typing** vs **structural typing**:
	- The main difference is that structural typing is enforced during static analysis found in statically typed languages, while duck typing is a runtime phenomenon emerging from the object semantics of dynamically typed languages.


#### TypeScript vs Java/C#

- TypeScript’s type system is structural, not nominal.

```typescript
interface Pointlike {
  x: number;
  y: number;
}
interface Named {
  name: string;
}
 
function logPoint(point: Pointlike) {
  console.log("x = " + point.x + ", y = " + point.y);
}
 
function logName(x: Named) {
  console.log("Hello, " + x.name);
}
 
const obj = {
  x: 0,
  y: 0,
  name: "Origin",
};
 
logPoint(obj);
logName(obj);
```

- When checking type, TS check if object have all properties of Empty have, so that object d in example bellow is allowed.

```typescript

class Empty {}

function testA(obj: Empty): void {}
testA(c);

const d = {a : 100}
testA(d);

```

- If two class have the same properties, they are find to use interchangably.

```typescript

class Car {
  drive() {
    // hit the gas
  }
}
class Golfer {
  drive() {
    // hit the ball far
  }
}
// No error?
let w: Car = new Golfer();

```

- Type erasure can be explained as the process of enforcing type constraints only at compile time and discarding the element type information at runtime.

- For example:

```java
public static  <E> boolean containsElement(E [] elements, E element){
    for (E e : elements){
        if(e.equals(element)){
            return true;
        }
    }
    return false;
}

// Will be compile to 

public static  boolean containsElement(Object [] elements, Object element){
    for (Object e : elements){
        if(e.equals(element)){
            return true;
        }
    }
    return false;
}

```

#### Funtional programming in TS

- Structural typing

```typescript

// @strict: false
let o = { x: "hi", extra: 1 }; // ok
let o2: { x: string } = o; // ok o2 = { x: "hi", extra: 1 }

```

- o2 only check if o have all properties it needs.
- Concepts similar to Haskell
	- Type aliases:

```typscript

type Size = [number, number];
let x: Size = [101.1, 999.9];

```

- Discriminated Unions:

```typscript

type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; x: number }
  | { kind: "triangle"; x: number; y: number };
 
function area(s: Shape) {
  if (s.kind === "circle") {
    return Math.PI * s.radius * s.radius;
  } else if (s.kind === "square") {
    return s.x * s.x;
  } else {
    return (s.x * s.y) / 2;
  }
}
```

#### Unions and Intersections

```typescript
type Human = {
    name: string;
    speaks: boolean;
};

interface Dog {
    name: string;
    barks: boolean;
}

type HumanAndDog = Human & Dog; // Intersection
type HumanOrDogOrBoth = Human | Dog; // Union

```

#### Declaration merging
- Two interface can have the same name, they will be merge into 1 big interface.

```typescript
interface Person {
    name: string;
}

interface Person {
    age: number;
}

type Num = numbers;
type Num = string; // Duplicate identifier Num

let user: Person = {
    // has the properties of both instances of Person
    name: "Tolu",
    age: 0,
};
```

- Otherwise syntax to extend for type is differ:

```typescript

type A = { x: number };
type B = A & { y: string };

type A = {
    x: number;
};

interface B extends A {
    y: string;
}

```

- Both type and interface can be implemented by class

```typescript
// Interface being implemented by a class
interface A {
    x: number;
    y: number;
}

class SomeClass implements A {
    x = 1;
    y = 2;
}

// Type alias being implemented by a class
type B = {
    x: number;
    y: number;
};

class SomeClass2 implements B {
    x = 1;
    y = 2;
}
```

- **Summary**:
	- **Similarity**: can be extend and implement, both use to declare the shape of object.
	- **Difference**: we can declare the same interface, all interface will be merged into one.


- **tsc**: when ts compile to js, it erase type and downleveling code:

```typescript

// Erase type

function greet(person, date) {
    console.log("Hello ".concat(person, ", today is ").concat(date.toDateString(), "!"));
}
greet("Maddison", new Date());

// Down leveling

`Hello ${person}, today is ${date.toDateString()}!`;
// Compile to 
"Hello " + person + ", today is " + date.toDateString() + "!";

```

#### Object type

```typescript

// The parameter's type annotation is an object type
function printCoord(pt: { x: number; y: number }) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
printCoord({ x: 3, y: 7 });

// Optional properties

function printName(obj: { first: string; last?: string }) {
  // ...
}
// Both OK
printName({ first: "Bob" });
printName({ first: "Alice", last: "Alisson" });


```

	
	  