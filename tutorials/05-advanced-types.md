# More on types

## Utility types
When you're dealing with data, frequently you might find that you want to impose restrictions or add flexibility to a type or class.  For example, if you want to update a user in the database, you might only receive some information and not everything required.  You could theoretically define two types: one for the general User itself with some fields required, and another where nothing is required.  But that's duplication, and we don't want to repeat ourselves!  What we can do instead is use a utility type.  A **utility type** is a built-in TypeScript type that transforms one data type to another.

Here's a demonstration with the `Partial<Type>` data type:
```ts
type User = {
    firstName: string,
    middleName?: string,
    lastName: string,
    email: string
}
type UpdatedUser = Partial<User>;
let newUser: User = {
    firstName: "Jack",
    lastName: "Doe",
    email: "jackdoe@email.com",
}
let updatedInfo: UpdatedUser = {
    firstName: "John",
    email: "johndoe@email.com"
}
newUser = {...newUser, ...updatedInfo}; // Grab old info first, then update new info
```
The `Partial` type takes in a type `T` and turns all the fields optional.  Conversely, if you want to make every field required, use the `Required<T>` type.

You might find that you want to create types that are different forms of the original type by a subset of the original keys.  You can accomplish this by using either the `Pick<Type, Keys>` or `Omit<Type, Keys>` utility types.

The `Pick<Type, Keys>` utility type allows you to select the keys you want to keep from the original Type.  The keys can be a single string literal or a union of string literals.  

The `Omit<Type, Keys>` utility type conversely lets you select the keys you wish to NOT retain.  Like the `Pick` union type, the keys can be a single string or a union of them.

Let's demonstrate how to use the `Pick` and `Omit` types:
```ts
type SimpleUser = Omit<User, "email" | "middleName">;
type AlternateSimpleUser = Pick<User, "firstName" | "lastName">;
```
Both examples above result in the same type of `User` with only the `firstName` and `lastName` retained.  Whether you use `Pick` or `Omit` depends on whether it's simpler to specify the fields you want or the fields to drop.  

Don't forget you can define your own type that's a union literals if you find you'll reuse it a lot.  You can leverage the union literal when you use the `Pick` and `Omit` utility types, like so:
```ts
type SimpleNameFields = "firstName" | "lastName";
type UserName = Pick<User, SimpleNameFields>;
```

If you don't want to risk the possibility of overwriting data, you can use the `Readonly<T>` utility type, like so:
```ts
type ReadonlyUser = Readonly<User>;
let userOne: ReadonlyUser = {
    "firstName": "Jane",
    "lastName": "Jones",
    "email": "janejones@email.com"
}
userOne.firstName = "Jill"; // ILLEGAL!!!
```
The `ReadOnly` type is especially useful if you're dealing with data from a database that you don't want to accidentally overwrite if it'll be sent back.

If you wish to create a new object, and you know what keys you want, and the type will be the same across the board, you can use the `Record` utility type.  The `Record<Keys, Type>` utility type allows you to specify the keys you want in the new type, along with the type of values you wish to link to each key.

For example, if you have a series of Employees, you can create a series of them like so:
```ts
type EmployeeName = string;
type EmployeeInfo = {
    id: number,
    title: string
}
type Employee = Record<EmployeeName, EmployeeInfo>; // Construct object type with key EmployeeName (string) and value EmployeeInfo (an object)
let employee1: Employee = {
    "Sam": {
        id: 5,
        title: "Boss"
    }
}
let allEmployees: Employee[] = [
    {
        "David": {
            id: 10,
            title: "Salesperson"
        }
    },
    {
        "Joan": {
            id: 15,
            title: "Salesperson"
        }
    }
]
```

There are other utility types out there, but these are the main ones you'll use most.  You can read more on utility types by visiting
https://www.typescriptlang.org/docs/handbook/utility-types.html.

To summarize the main utility types:
- Partial<DataType> - makes ALL fields optional
- Required<DataType> - makes ALL fields required, even those that would defined as optional in the original definition of the data type
- Pick<Type, Keys> - create a type where we can pick the keys we want from the given data type
- Omit<Type, Keys> - create a type where we decide which keys we DON'T want (the ones to omit)
- Readonly<DataType> - ensure the values CANNOT be changed (become immutable)
- Record<Keys, Type> - create an object type whose keys are Keys and whose values are Type

## Generics
When you want to design a component, a library or anything other code that is to be reused, generics are the best way to go.  **Generics** are effectively type variables that allow to create reusable functions, classes, etc. with any type(s) you wish.  Believe it or not, you've seen generics already when we talked about utility types!

Imagine you want to pick a random item from an array.  Let's assume it consists only of numbers.  You can write a function like so to accomplish the goal:
```ts
function pickItem(arr: number[]): number {
    let randomInt: number = Math.floor(Math.random() * arr.length);
    return arr[randomInt];
}
```
HOWEVER, you will find this will likely not compile!  This is because when you want to access an item at an index that's derived from an expression like in the example above, there's a possibility that the value will be an index outside the bounds of the array.

Another issue that arises is that this function would only work for arrays of numbers.  What if you have arrays of strings?  Objects?  Class instances?

Generics come to the rescue!  Now we can insert generics like so:
```ts
function pickItem<T>(arr: T[]): T {
    let randomInt: number = Math.floor(Math.random() * arr.length);
    return arr[randomInt];
}
```
Now this can work for any type we use, like `number`, `string`, a custom type like `User`, etc.  However, we still have an issue because this could return undefined.  So we can use a generic type predicate to ensure the index is valid:

```ts
function isValidIndex<T>(arr: T[], index: number): index is number {
    return index >= 0 && index < arr.length && Number.isInteger(index); // Index is in valid range and it's an integer
}
```

```ts
function pickItem<T>(arr: T[]): T | undefined {
    let randomInt: number = Math.floor(Math.random() * arr.length);
    if (isValidIndex<T>(arr, randomInt)) {
        return arr[randomInt];
    }
    return undefined;
}
```
To deal with the undefined issue as we could have an array that's empty, we say that we will return an item or `undefined`.  Notice the type check to ensure that we have a valid index.

When you call the function, you can specify the type if you wish.  It's recommended to do so, especially with more complex types and classes.  Here's how you can call the function:
```ts
let z: string[] = ["Green", "Blue", "Red"];
let x: number[] = [1, 2, 8, 4];
console.log(pickItem<string>(z)); // Type is explicitly mentioned
console.log(pickItem(x)); // Also works
```
You can use types in interfaces and classes as well!  And you can use multiple generics.

If you want to restrict your types a bit based on certain properties, you can do so inside the angled brackets `<>`.  Here's an illustration:
```ts
function identityWithLengthProperty<T extends { length: number }>(item: T): T {
    console.log(item.length); // Display the number of items
    return item; // Return the item back; identify functions return the original input
}
console.log(identityWithLengthProperty("Hello there!")); // Okay
console.log(identityWithLengthProperty([3, 5, 2])); // Okay
// console.log(identityWithLengthProperty(true)); // ILLEGAL
```

## Resources
- "Effective TypeScript" book by Dan Vanderkam
- https://basarat.gitbook.io/typescript/ - TypeScript Deep Dive