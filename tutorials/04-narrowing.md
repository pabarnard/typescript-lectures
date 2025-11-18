# Narrowing and type checking

Oftentimes it's important to make sure you're dealing with the type when processing data.  So in order to minimize the possibility of an issue arising at runtime, we can **narrow** a type down to a more specific one or even a more general one if needed and then perform operations as needed.

## Type assertions
When you get data in which the data type is not quite what you want or is expected, you can perform a **type assertion**, which allows you to cast a variable to another data type.  For example, if you call on a method that returns an `HTMLElement` but you need it to be more specific, like an `HTMLInputElement`, you can do so:
```ts
// Assume there's a variable called nameInput thats an HTMLElement
const moreSpecificNameInput = nameInput as HTMLInputElement;
const alternateWayForNameInput = <HTMLInputElement>nameInput; // NOTE: This does NOT work in .tsx files
const directNameInput = document.getElementById("test") as HTMLInputElement; // Directly cast after calling on a function or method; in this case getElementById returns an Element (HTMLElement), which is very generic
const directNameInput = <HTMLInputElement>document.getElementById("test"); // Alternate approach; doesn't work in .tsx files
```
WARNING: Type assertions are not kept when compiled into JavaScript, so there's no run-time checking.  So make sure your assertions are correct!!

## Type guards
**Type guards** are used to narrow down the scope of a variable, especially if a parameter in a function or method is a union of types.

### Primitives and other basic types: typeof keyword
In JavaScript, you can check the data type of a variable by using the `typeof` keyword.  Unfortunately, this is not very useful when dealing with objects, arrays, classes and many other data types.  If you tried doing `typeof x`, where `x` is an array, an object or an instance of a class, you get `"object"` back in each case, which isn't very useful.  

The `typeof` keyword is useful for checking for numbers, booleans, strings and undefined values.  You can read more from the JavaScript MDN [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof) and from TypeScript [here](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#typeof-type-guards).

### Arrays
For arrays, there are a couple of ways to add type guards.  One way is to check if a variable is even an array by using the `isArray` static method from the `Array` class:
```ts
function countChars(x: string | string[]): number {
    let count: number = 0;
    if (Array.isArray(x)) { // Check if a variable is an array or not
        // Do stuff here, as x is now of string[]
    } else {
        // Do stuff here for x, which is a plain string type
    }
    return count;
}
```

Another type check is to see if every element of an array is a specific type, and you can leverage the `every` method for arrays like so:
```ts
let x1: (string | number)[] = [1, "green", 3];
let x2: (string | number)[] = [1, 5, 3];
console.log(x1.every(item => typeof item === "number")); // false
console.log(x2.every(item => typeof item === "number")); // true
```

### Classes: instanceof keyword
When you're dealing with classes, it's very useful to check the type of class for a variable.  This is especially useful if you need to call on a specific attribute or method tied to the class.  The way we can check is with the `instanceof` keyword:
```ts
class Phone {
    screenSize: number;
    constructor(screenSize: number) {
        this.screenSize = screenSize;
    }
}

class Android extends Phone {
    osName: string;
    constructor(screenSize: number, osName: string) {
        super(screenSize);
        this.osName = osName;
    }
}
let genericPhone = new Phone(4);
let myAndroid = new Android(6, "Pie");
console.log(genericPhone instanceof Phone); // true
console.log(genericPhone instanceof Android); // false
console.log(myAndroid instanceof Phone); // true - note that because we're inheriting from the Phone class, any Android is also a Phone
console.log(myAndroid instanceof Android); // true
```
(Technically the `instanceof` operator checks whether the prototype chain of a variable contains `ClassName.prototype`, where `ClassName` is the class you're checking against.  You don't need to worry too much about prototype chains unless you're getting into advanced JavaScript/TypeScript.)

### Objects: in keyword
When dealing with objects, you might not know if it contains a specific property.  The `in` keyword allows you to determine if a variable is of a specific type, like so:
```ts
interface Animal {
    name: string;
    makeSound: () => void;
    hasFur: boolean;
}
interface Plant {
    name: string;
    grow: () => void;
    color: "green";
}

function determineSpecies(creature: Animal | Plant) {
    if ("hasFur" in creature) { // creature is an Animal
        console.log(`This is an animal ${creature.hasFur ? 'with' : 'without' } fur!`);
    } else { // creature is a Plant
        console.log("We have a plant!");
    }
}
```
Be a little careful with optional parameters, as illustrated in the example below from TypeScript's documentation:
```ts
type Fish = { swim: () => void };
type Bird = { fly: () => void };
type Human = { swim?: () => void; fly?: () => void };
 
function move(animal: Fish | Bird | Human) {
    if ("swim" in animal) {
        animal; // Fish | Human
    } else {
        animal; // Bird | Human
    }
}
```

## Discriminated unions
When you deal with unions of types, you often will check to see which type you're dealing with.  For example, you might have these types:
```ts
interface DiceGame {
    piece: 'dice',
    name: string,
    values: number[],
    isMultiplayer: boolean,
    usesBoard: boolean
}

interface VideoGame {
    piece: 'controller',
    name: string,
    buttons: string[],
    isMultiplayer: boolean
}

interface PaperGame {
    piece: 'pencil',
    name: string,
    isMultiplayer: boolean,
    usesBoard: boolean
}

type Game = DiceGame | VideoGame | PaperGame;
```
Notice how each type has a common property with different values - in this case it's the key `piece`.  This property is called a **discriminant property.**  Combining multiple types with the same property is a **discriminated union.**  If you create a function that takes in a `Game` parameter as an argument, you'll want to determine the type of game based on the piece - in other words, discriminate the type, and one common approach is a switch statement like so:
```ts
function playGame(game: Game): void {
    switch (game.piece) {
        case 'dice':
            // game is of the DiceGame type
            break;
        case 'controller':
            // game is of the VideoGame type
            break;
        case 'pencil':
            // game is of the PaperGame type
            break;

    }
}
```
You can also do a series of `if` statements to determine the type if you wish.

## Check for optional values
If you're dealing with attributes or values that might be optional, like an optional key in an object or a union of objects, it's a good idea to check if there's a value supplied.  The same idea applies to optional parameters in functions and methods.  To check if a value is supplied, you can use the `typeof` operator or the `?` operator or check for `undefined`, depending on the situation:
```ts
interface Human {
    firstName: string,
    middleName?: string,
    lastName: string
}
let johnDoe: Human = {
    firstName: "John",
    lastName: "Doe"
}
console.log(johnDoe.middleName?.toUpperCase()); // Check if there is a middle name when chaining

console.log(typeof johnDoe.middleName); // "undefined"

console.log(johnDoe.middleName === undefined);
```
**WARNING:** If you're dealing with a possible null value, you should check if it's null by checking for equality to null by doing `variableName === null`.  Do NOT use the `typeof` operator!! 

## Type predicates ("is" keyword)
Whenever you deal with more complex data types and classes, sometimes using built-in methods may not be sufficient for narrowing types, and this is especially true for larger codebases.  This is where type predicates come in handy.  A **type predicate** is a statement that returns a boolean value for if a variable is of a particular type or class.  (A predicate function in programming is a function that returns a true or false value based on whether a condition or set of conditions is met.)

The syntax for a type predicate looks like this if you have a variable defined as a union of three different classes:
```ts
function checkIfThisItemIsTree(item: Tree | Flower | Grass): item is Tree {
    return item instanceof Tree;
}
```
Notice the return statement that checks if the variable is a `Tree`.  The line that defines the function contains the statement `item is Tree` after defining the function, so the variable is predicated on being the type `Tree`.

If you're dealing with objects, you can use type predicates to narrow down if a field or set of fields exists.  You can even check if an interface is implemented.

The power of type predicate functions like the one above lies in its reusability if you often need to check if a variable is of a particular type.