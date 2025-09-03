# Introduction to TypeScript

Welcome to this introduction to TypeScript.  In order to use this tutorial, make sure you have the basics of programming down (e.g. if statements,for loops, while loops, functions and methods, etc.).  If not, I recommend playing around with JavaScript (preferably) or a programming language of your choice first.

## Motivation
Imagine that you have a function in JavaScript that returns the sum of two values like so:
```js
const addNums = (a, b) => {
    return a + b;
}
```
Now when you call the function by passing in two numbers, e.g. 3 and 4, you get 7 back as expected:
```js
let sum = addNums(3, 4); // sum is 7
```
Now imagine that someone passes in two variables that are strings:
```js
let str1 = "Hello", str2 = "there";
let result = addNums(str1, str2); // result is "Hellothere"
```
While not the intention of the function, it will still work, as strings can be concatenated together.

Now suppose you have two variables of different types, and at least one of them is not a string or number.  Try this and see what happens:
```js
let foo = new Map();
let bar = new Set();
addNums(foo, bar); // You get an unusual result: '[object Map][object Set]'
```
If you don't know the data types of the variables, you'll get *at best* a weird result or *at worst* a runtime error!  It doesn't make sense to add a set to a map!

So it would be nice to add some guards to ensure we have consistent behavior, and adding types will certainly help.

## Strongly typed vs. weakly typed languages (statically vs. dynamically typed)
JavaScript is a *dynamically typed* language in that the data types are determined as the program is run and not predefined beforehand.  This gives the language a great deal of flexibility, but opens the door to runtime errors due to mismatching types.

Other languages, such as C# and Java, are *statically typed*.  This means that each variable must have a data type when it's defined immediately.  Usually statically typed languages run faster and are less prone to runtime errors as compilers will catch type errors before the code is even run.l

For example, if you wanted to define an integer called `x` in JavaScript, you would do this:
```js
let x = 10; // Alternately can use "const" ("var" not recommended)
```
In Java, the same variable is defined this way:
```java
int x = 10; // Notice the "int" so that the variable x is an integer data type
```

Is it possible to add type guards to JavaScript?  The answer is **yes!**  This is where TypeScript comes to the rescue!

## Introduction to TypeScript with set-up
[TypeScript](https://www.typescriptlang.org/) is a superset of JavaScript that adds static typing.  So we keep the features and flexibility of JavaScript while allowing us to declare types for variables, functions and methods and much more!

Before starting a new TypeScript project, make sure the following are installed:
- [Node.js](https://nodejs.org/en/download)
- The npm package manager - this should automatically install when you install Node.js.
- npx package executer - this should automatically install when you install Node.js and npm.

Now to start a plain *TypeScript* project *without* using scaffolding tools like Vite:
1. Create a new project folder: `mkdir your-project-name`.
2. Go inside the project folder: `cd your-project-name`.
3. Start the project from inside the folder: `npm init -y`.  This will create a package.json file that will list various scripts that you can run (see the "scripts"), along with the dependencies installed via `npm i` to run your project ("devDependencies").
4. Install Typescript as a dependency: `npm install typescript --save-dev`.  If there's no package-lock.json file, it will be created once `npm i` or `npm install` is run at least once.
5. Create a TypeScript project configuration file called tsconfig.json: `npx tsc --init`.  There are a ton of options found inside for compiling your files, and feel free to play around with options like "strictNullChecks"!  Now you can build a project with TypeScript!  (More information on the tsconfig file can be found at https://www.typescriptlang.org/docs/handbook/tsconfig-json.html and https://www.typescriptlang.org/tsconfig/.)
6. Anywhere inside the project folder, you can create a typescript file by typing `yourFileName.ts` - notice the `.ts` extension!  You can create as many TypeScript files as you wish.
7. Go into the "package.json" file and add the following to the "scripts" key: `"yourShortcut": "tsc && node fileToRun.js"`.  This will create a JS file with the same name as the TS file, along with other files that end in ".d.ts" and ".d.ts.map".  (Those files are type declaration files for JavaScript and map declaration files for mapping type declarations back to your TS files, respectively.)  NOTE: Make sure all your scripts are key-value pairs, separated by commas!  For example, you could add `"dev": "tsc && node test.js"`, which will compile ALL your TS files and then run the JS version of your test.ts file (assuming you have a file with that name; make sure you change the file name accordingly).
8. Now in your terminal, type `npm run dev` or `npm run yourShortcut` (depending on what your key is called) to compile and run!

You're free to only compile by just having `tsc` instead of `tsc && node yourFile.js` in your scripts, and then you can run your own JS files individually with `node yourFile.js`.  Do NOT run `tsc` in the terminal by itself.  If you want to compile a single file, run `npx tsc fileName.ts` and then run `node fileName.js`.  Alternately you can compile and then run by typing `npx tsc fileName.ts && node fileName.js`.

## Primitive types
JavaScript has several primitive types - `string`, `boolean`, and `number` are the most common ones, although there are others out there (see https://developer.mozilla.org/en-US/docs/Glossary/Primitive).  With TypeScript we can assign data types to our variables like so:

```ts
let x: number = 20; // This is an explicit declaration of a data type
let y = "Hello!"; // This is an implicit declaration of a data type
```
There are other types out there, such as `any`, `unknown` and `never`.  You should **never** use `any`, as that removes type checking!

**WARNING:** Primitive variables cannot be assigned a value of `null` or `undefined` if a type has been explicitly defined.  For example, if you have `let z: boolean = undefined;`, this is ILLEGAL!

Where primitives and other types shine are when you create functions and methods.  Revisiting the `addNum` function from before, we can now add types!
```ts
const addNums = (a: number, b: number): number => { // Notice the ": number" here!
    return a + b;
}
```
Notice how each parameter, `a` and `b`, has a type explicitly written out, while we also specify the data type for what we're returning.  If we tried to pass in an argument that's not a number, we will get an error.  Try `addNums("hi", 5)` and see what happens.  If you hover over the word "hi", you'll see a message that says `Argument of type 'string' is not assignable to parameter of type 'number'.`.  If you attempt to compile, you will get an error, preventing runtime errors!

We can also choose not to return anything from a function:
```ts
const greet = (name: string): void => {
    console.log(`Hello ${name}!`);
}
```
If you're confused by what's happening here, don't worry.  We'll talk about functions more soon!

## Arrays and tuples
In JavaScript, TypeScript and many other languages, an **array** is a data structure used to hold a collection of items together.  They're defined with the square brackets like so:
```js
let myArray = [3, 8, "blue", true, -4.5];
```
An array can have any number of items, and it can be empty: `[]`.  Note that to access items, we start at index 0, NOT index 1:
```js
console.log(myArray[0]); // 3
let ind = 4; // You can use variables to hold valid indexes
console.log(myArray[ind]); // -4.5
```
You can have mixed data types as you see in the variable `myArray` above.  Note in other languages you might not have this flexibility!

In TypeScript if you know that the data type will be the same for each entry in the array, you can enforce that type like so:
```ts
let myNumbers: number[] = [3, 5, -2, 1, 5]; // All items in the array MUST be numbers
```
You can actually specify multiple data types in an array.  If you know how many items are in the array, and you know their data types, you can define a **tuple**.  Here's an example of a tuple:
```ts
let thisUser: [number, string];
thisUser = [1, "Jane"]; // Notice how the data types must match!
```
Here we have a variable called `thisUser` that holds a number at index 0 and a string at index 1.
You can have an array of tuples like so:
```ts
let allUsers: [number, string][] = [[1, "Jane"], [2, "Peter"]]; // An array of tuples
```
This is very useful for holding information from databases and APIs!

## Types and interfaces (objects)
Imagine that you have a tuple or some other combination of types that is reused a lot in your code.  For example, suppose that a person saved in a database comes back with an ID, a name, and a title.  So you could do this:
```ts
let usersFromDB: [number, string, string][];
```
But if you wind up having multiple variables that are `[number, string, string]`, it would be unwieldy to re-type it repeatedly.  This is where type aliases come to the rescue!  A **type alias** is assigning a name (an alias) to a collection of types.  In our example,
```ts
type Person = [number, string, string];
```
So we've defined a new type called `Person` that is derived from other types.  Notice the keyword `type` at the beginning.  Type aliases are basically like variables, except we use types.

Now the variable `usersFromDB` can be refined like so instead:
```ts
let usersFromDB: Person[]; // Now we can use the Person type alias instead!
```
An **interface** is a type alias that uses objects.  This is used a lot in JSON data.  For example:
```ts
interface PhotoInfo {
    caption: string, // Each key-value pair is a property with a type (or collection of types) attached
    photographer: string,
    createdOn: Date
}

let myPhoto: PhotoInfo = {
    caption: "Photo of me",
    photographer: "Jack",
    createdOn: new Date()
}
```
When an interface is defined, we specify the data types of each key.  Then when variables use the interface, they must follow the typing specified.

You can also specify anonymous functions like so:
```ts
interface MyInterface {
    clean: () => void // Notice the function has no inputs and doesn't return anything
}
```

**IMPORTANT:** Types CANNOT be changed after they're defined, while interfaces can have additional items appended.  In other words, you can extend interfaces to add more properties.  (More info on this is in the next section.)

## Enumerations
Suppose you know to assign values to variables in a specific way.  For example:
```ts
let north = 0, east = 1, south = 2, west = 3; // Types inferred in this case
```
Now if you need to check for a value, you might do this:
```ts
let direction: number = 0;
if (direction === north) {
    console.log("Moving north");
}
```
It might be bit difficult to read this code when integers are used.  But we can enumerate the values ourselves like so:
```ts
enum Direction {
    North, // Default value is 0
    East, // Default value is 1
    South, // Default value is 2
    West // Default value is 3
}
```
This is basically an interface, except the keyword `enum` is used.  By default, the values start at 0 and increment upwards by 1.

You can specify a custom starting value like so:
```ts
enum Direction {
    North = 1,
    East, // 2
    South, // 3
    West // 4
}
```
You can also assign each value individually:
```ts
enum Genre {
    Fiction = 1,
    Biography = 5,
    Nonfiction = 10,
    Romance = 20,
    Thriller = 30
}
```
You can even use different data types other than numbers:
```ts
enum UserType {
    Super = "SUPER",
    Admin = "ADMIN",
    User = "USER"
}
```
It's *highly* recommend that you use the same data type for each member.

Now we can revisit the direction code:
```ts
enum Direction {
    North,
    East,
    South,
    West
}
let currentDirection = Direction.North; // Implicit type declaration here
if (currentDirection === Direction.North) {
    console.log("Moving north");
}
```
This is MUCH easier to read and understand!  More info can be found here: https://www.typescriptlang.org/docs/handbook/enums.html

## Optional values
You can add optional keys and types with the `?` like so:
```ts
let Name = [string, string, string?]; // The last item is optional; all optional types must come at the end
let myName: Name = ["Jack", "Doe"];
let anotherName: Name = ["Jane", "Q", "Public"];
```
```ts
interface House {
    address: string,
    color: string,
    squareFeet: number,
    floors?: number
}
let myHouse: House = {
    address: "123 Main St.",
    color: "yellow",
    squareFeet: 1200
}
let secondHouse: House = {
    address: "456 Main St.",
    color: "blue",
    squareFeet: 1600,
    floors: 2
}
```

## Unions (a | b), intersections (a & b) and literal types (1 | 2 | 3)
While previously a variable could be one data type, it's still possible to make it hold more than one.  Here's an example:
```ts
let myStr: string,
myStr = "Hi there!"; // Good
myStr = null; // ILLEGAL
let possibleStr: string | null
possibleStr = null; // Now OK
possibleStr = "Yay!";
```
The **union** operator `|` allows for a variable to hold more than one data type.  You can have as many types as you wish.

Unions are used a lot in functions when we need some flexibility.  (This will come in a future lesson, so stay tuned!)

You could find that you want to combine interfaces.  Here's an illustration:
```ts
interface Appliance {
    wattage: number,
    usesBatteries: boolean
}

interface Microwave {
    name: string
}
type PoweredAppliance = Appliance & Microwave; // The '&' operator combines the interfaces
let myMicrowave: PoweredAppliance = {
    name: "GE",
    wattage: 1200,
    usesBatteries: false
}
```
The **intersection** operator allows us to combine the attributes from two types.

In a future lesson, we'll talk about inheritance with interfaces and classes.  More info can be found on inheritance vs. intersection here: https://www.typescriptlang.org/docs/handbook/2/objects.html#interface-extension-vs-intersection

If you know the possible values a variable can hold, you can use a **literal type** like so:
```ts
let currentCityPark: "Central Park" | "Westlake Park" | "Williams Park"; // Literal type with the "|"
currentCityPark = "Central Park";
currentCityPark = "Downtown Park"; // ILLEGAL
```
A literal type is a union of fixed values.  These are used most in functions to restrict input and output values.  More will come in a future lesson!