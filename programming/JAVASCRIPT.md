# Javascript

## 1. Type coercion
- There are three type of type coercion:
  - To string 
  - To number
  - To boolean

## 2. Dynamic vs static type language 
- Dynamic type language: type is determine at runtime.
- Static type lanaguage: type is determine at compile time.


## 3. Higher order function:
A higher order function is a function that takes one or more functions as arguments, or returns a function as its result.

```javascript
// logic to clculate area
const area = function(radius){
    return Math.PI * radius * radius;
}


// logic to calculate diameter
const diameter = function(radius){
    return 2 * radius;
}

// a reusable function to calculate area, diameter, etc
const calculate = function(radius, logic){ 
    const output = [];
    for(let i = 0; i < radius.length; i++){
        output.push(logic(radius[i]))
    }
    return output;
}

```

## 4. Closure and memory in javascript


- A closure is a function that has access to variables from its outer lexical scope, even after the outer function has returned. 

```javascript
function outerFunction() {
  const outerVar = 'Hello';

  function innerFunction() {
    console.log(outerVar);
  }

  return innerFunction;
}

const myInnerFunction = outerFunction();
myInnerFunction(); // outputs 'Hello'

```

- Usage of closure:
  - For private variable
  - For cache result

 ## 5. Prototype inheritance in javascript

 ```javascript
let subject = {
  topic: "javascript",
  about: function () {
    console.log("JS is amazing");
  },
}; //base class

let course = {
  __proto__: subject,
  instructor: "professor",
}; //inherited from subject

let department = {
  __proto__: course,
  dept_name: "IT",
}; //inhserited from course

let student = {
  __proto__: department,
  id: 1,
}; // inherited from department

console.log(student.dept_name);
 ```

## 6. Promise in javascript

- allSettled
```javascript
async function() {
  const promises = [
    fetch('/api.stackexchange.com/2.2'), // succeeds
    fetch('/this-will-fail') // fails
  ];

  const result = await Promise.allSettled(promises);
  console.log(result.map(promise => promise.status));
  // ['fulfilled', 'rejected']
}
```