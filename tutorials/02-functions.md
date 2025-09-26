# Functions

## Defining functions and methods
In programming, a **function** is a body of code that is available on call.  (When you define a function inside a class, it's called a **method.**  We'll talk more about classes down the road!)  It's akin to a recipe, which contains a set of ingredients and instructions to cook some food.  The ingredients are required to make the recipe, and it's the same idea for a function.  Functions more often than not have **parameters**, which are variables passed in that are to be used.  You could make 12 cookies, 24 cookies, 36 cookies, or any amount you wish.  A parameter in this case would be the number of cookies to bake.

Here's a function in JavaScript:
```js
function bakeCookies(num) { // The variable "num" is a *parameter*!
    // Do something with cookies here
}
// ES6 (more modern) version:
const bakeCookies = num => {
    // Do something with cookies here
}
```
You can pass in as many parameters as you wish - or not pass any in at all.

Functions often *return* values, which can then be used elsewhere in the code.  NOTE: returning a value is NOT the same as printing it to the console!  Returning allows you to use the contents elsewhere, whereas printing to the console only allows you to take a look at it without being able to interact with it.  Think of it like ordering food from a restaurant: when you order, you give some money and then receive food in return.  But if you look at the food being prepared on camera, and then you only can look at it once it's prepared, you can't do anything with it - let alone eat!

Here is an example of using the contents from a function elsewhere:
```js
const buildHouse = (bedrooms, color) => {
    return {
        "building": "House",
        "bedrooms": bedrooms,
        "color": color
    }
}
const newHome = buildHouse(4,"blue"); // Save the result from a function
console.log("Adding a bedroom:");
newHome.bedrooms++; // Now we can do stuff with the item!
console.log("Now the house has " + newHome.bedrooms + " bedrooms!");
```

In TypeScript, you need to specify types for your parameters AND the return type.  Here's an example:
```ts
function addNumsV1(a: number, b: number): number { // Notice how we specify data types for each parameter, then the type for the return value.
    return a + b;
}

const addNumsV2 = (a: number, b: number): number => {
    return a + b;
}

let sum = addNumsV2(5, 8); // sum is now a number variable
```
If you don't wish to return anything from a function, use the word "void":
```ts
function greet(name: string): void {
    console.log(`Hello ${name}!`);
}
```

## Type aliases with functions
Often you will find that you will want readability in your code.  Imagine the following:
```ts
const calculateDistance = (p1: [number, number], p2: [number, number]): number => {
    return Math.sqrt((p2[0]-p1[0])**2 + (p2[1]-p1[1])**2);
}
```
If you're dealing with several parameters, and the data types for each are complicated, it will make your code a lot less readable!  So let's add a type alias:
```ts
type Point = [number, number];
const calculateDistance = (p1: Point, p2: Point): number => {
    return Math.sqrt((p2[0]-p1[0])**2 + (p2[1]-p1[1])**2);
}
```
Now it's a lot more intuitive what the two parameters represent!  Imagine if you're passing in objects as parameters - it's much better to use type aliases than writing out each object's properties for each parameter!

## Unions and literal types with functions; narrowing
Suppose you want a little bit of flexibility with your parameters, but you don't want to use the `any` keyword.  You can utilize unions that allow some flexibility in what you're passing in and what you're returning!  Here are some examples:
```ts
const countCharacters = (item: string | string[]): number => {
    if (Array.isArray(item)) { // Here we perform narrowing to zero in on the type of the parameter, which affects the operations we can perform
        const totalCharacters: number = 0;
        for (const str of item) {
            totalCharacters += str.length;
        }
        return totalCharacters;
    } else { // Here item is a string only and NOT an array of strings
        return item.length;
    }
}

const processNumOrStr = (mysteryItem: string | number): void => {
    if (typeof mysteryItem === "string") {
        console.log("You passed in a string with the value " + mysteryItem); // mysteryItem is a string
    } else {
        console.log(`Your number is ${mysteryItem}`); // mysteryItem is a number
    }
}

const errorHandler = (errorCode: "400" | "404" | "418"): void => { // Using literals
    if (errorCode === "400") {
        console.log("Handling bad request!");
    } else if (errorCode === "404") {
        console.log("Process not found error here");
    } else {
        console.log("I'm a teapot!");
    }
}
```
Two of the most common ways to narrow a variable with multiple possible data types is to use the `typeof` keyword and the `Array.isArray` method.  The `typeof` keyword is used primarily for strings, numbers, booleans, and for checking if a variable is defined.  It's usually NOT used with arrays and objects because using that operator results in "object" in both cases.
We'll learn more about narrowing in future lessons, but for more reading, check this document out from TypeScript: https://www.typescriptlang.org/docs/handbook/2/narrowing.html.

## Optional parameters and keys
JavaScript and several other languages allow for parameters to be *optional.*  TypeScript retains this flexibility.  When you define optional parameters, they must be placed last, and each parameter must have a "?" at the end of the name.  Here's an example:
```ts
function greet(firstName: string, lastName?: string): string { // Notice how lastName is at the end and is optional thanks to the "?" symbol
    // console.log(lastName); // lastName is "string | undefined" here
    if (typeof lastName === "undefined") {
        return "Hello " + firstName + "!"; // lastName is undefined only here
    } else {
        return "Hello " + firstName + " " + lastName + "!"; // Now lastName is a string only thanks to narrowing
    }
}
```
Optional keys also apply with interfaces and objects:
```ts
interface Person {
    firstName: string,
    middleName?: string,
    lastName: string,
    isHappy?: boolean,
    siblings?: {
        firstName: string,
        lastName: string
    }[]
}
```
This makes the "isHappy" property optional when defining Person objects.  You can also check if a property exists and then perform operations accordingly:
```ts
const me: Person = {
    firstName: "James",
    lastName: "Jones",
}
console.log(me.middleName?.toUpperCase()); // "undefined" in this case, but if we had a middle name, then we see it converted to all caps
console.log(me.hasOwnProperty("middleName")); // false (can also do `"middleName" in me`); there are other ways
```
If an optional key holds an object, then you can nest accordingly and check along the way: `me.siblings?.length` to count the number of siblings, or get `undefined` if the key `siblings` doesn't exist.  This is called *optional chaining*.

Performing optional checking is very useful!  In React, you can use the "?" to conditionally render content.

You can forcibly perform an operation by using the bang "!" operator, but it's *VERY* dangerous because it ignores the possibility of a key being undefined.  For example, you could do `me.siblings!.length`, but it could lead to an error!

## Rest parameters (the spread operator inside a function)
To be added

## Function/method overloading
To be added