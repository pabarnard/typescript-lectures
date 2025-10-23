# Classes and inheritance

Many programming languages are actually object-oriented, like Java.  Basically all behavior is encapsulated in methods that are bound to objects.  JavaScript also allows for you to define your own classes, and TypeScript does too.

## Defining classes
Recall that interfaces are basically blueprints for objects with type checking.  But if we need more than that, especially if you need to create a lot of objects, then classes are better, and if you need to retain the blueprints, classes also work out better because interfaces are virtual in TypeScript and aren't retained when TypeScript files are converted to JavaScript.

Here is how to define a class in JavaScript:
```js
class YourClassNameHere {
    constructor(name) {
        this.name = name; // This class has attributes called "name" and "count"
        this.count = 0;
    }

    method1() { // And this class has instance methods - i.e. methods tied to one specific item that's of this class

    }

    method2() {

    }
}
```
**IMPORTANT:** Be careful NOT to use the arrow function when defining classes as the `this` keyword takes on different meaning.  Inside classes, `this` can refer to either an instance of the class or the class itself, depending on the type of method (instance vs. static).  (We'll talk more about static attributes and methods in a bit!)

Classes have constructor methods that serve as the blueprint for each object.  They have attributes, such as `name` and `count` in the example above, and they have methods that govern the behavior of the object, like `method1` and `method2`.

In TypeScript, you MUST define your attributes before the constructor like so:
```ts
class Vacuum {
    name: string; // Notice the attributes here
    power: number;

    constructor(name: string, power: number) {
        this.name = name;
        this.power = power;
    }

    clean(): void {
        console.log("Cleaning the house!");
    }
}

let vac1: Vacuum = new Vacuum("Oreck",125);
console.log(vac1.name); // Oreck
```
You can even define `readonly` attributes in TypeScript that can only be changed inside the constructor:
```ts
class Store {
    readonly name: string;
    constructor(name: string = "My Store") { // Notice we can define default parameters if desired
        this.name = name; // OKAY
    }
    changeName(newName: string): void {
        this.name = newName; // ILLEGAL: cannot change a readonly value
        return this; // To chain methods
    }
}

let myStore: Store = new Store("Main Street Market");
myStore.changeName("New Road Market").changeName("New Avenue Shop"); // Chaining methods
```
You can also chain methods by typing `return this` inside instance methods like `changeName` above.

## Static methods and attributes
Like object-oriented languages, you can define fields and methods that are tied to the class itself and not an *instance* - i.e., a specific object - of the class.  These are called **static** methods and attributes.

Here is how to define static methods and attributes:
```ts
class School {
    static district: string = "Springfield School District"; // Usually you want to assign default values for static attributes
    static totalEmployees: number = 0;
    name: string;
    studentCount: number;
    employeeCount: number;
    constructor(name: string, studentCount: number) {
        this.name = name;
        this.studentCount = studentCount;
        this.employeeCount = 0;
    }

    hireEmployee(): void { // *Instance* method
        this.employeeCount++; // "this" refers to an instance of the class
        School.totalEmployees++; // Notice the name of the class here and NOT the word "this"
        return this;
    }

    static renameDistrict(newName: string): void { // *Static* method
        this.district = newName; // "this" refers to the school class
    }
}

let lisaElementary: School = new School("Lisa Elementary School", 200);
lisaElementary.hireEmployee();
School.renameDistrict("Springfield County District"); // Notice for static methods and attributes we start with the name of the class!
console.log(School.district);
```

## Inheriting from other classes
In object-oriented programming (OOP), you have the ability to **inherit** from other classes.  Inheritance allows you to bring in attributes and methods from another class.  In JavaScript you can inherit classes, and so naturally can TypeScript.  

Let's imagine you have these classes in TypeScript:
```ts
class Dwelling {
    address: string;
    bedrooms: number;
    bathrooms: number;
    floors: number;

    constructor(address: string, bedrooms: number, bathrooms: number = 1, floors: number = 1) {
        this.address = address;
        this.bedrooms = bedrooms;
        this.bathrooms = bathrooms;
        this.floors = floors;
    }

    clean() {
        console.log("Cleaning dwelling place");
    }
}

class House extends Dwelling { // Inheriting from the Dwelling (parent) class; the House (child) class is a *subclass* of the Dwelling class
    hasBackyard: boolean;

    constructor(address: string, bedrooms: number, bathrooms: number = 1, floors: number = 1, hasBackyard: boolean = true) {
        super(address, bedrooms, bathrooms, floors); // Call on constructor from Dwelling class
        this.hasBackyard = hasBackyard;
    }

    clean() {
        super.clean(); // Call on Dwelling's clean method
        console.log("Phew - all done!");
    }
}
```
By inheriting classes, you significantly reduce the amount of workload needed to create instances of classes.  It's a fundamental part of not repeating yourself in code!

In the constructor in the House class, notice we're calling on the parent constructor with the `super` method to instantiate the fields that we're inheriting (address, bedrooms, bathrooms and floors).  We have to instantiate (assign) the hasBackyard attribute outselves since it's new.

When inheriting methods, we're not only grabbing attributes, we're also adding the methods defined in the parent class.  The *parent* class - Dwelling in our example - is the class we're inheriting from, while the *child* class - House in our example - is who's doing the inheriting.

We have the ability to override methods as well.  Notice in the `clean` method in the House class we are overriding the `clean` method from the Dwelling class.  If we still need the parent's version of a method, we can do `super.methodName()`, like `super.clean()` in the example above.

If we didn't define the `clean` method in the House class, then we can use the `clean` method from the parent class instead by default.  For example:
```ts
let myHouse: House = new House("123 Main St.", 3, 2);
myHouse.clean(); // As is, it prints "Cleaning dwelling place" then "Phew - all done!"
/*
But if we had NOT defined a clean method in the House class, we'd only get "Cleaning dwelling place"
*/
```
## Access control
By default all methods and attributes in classes are `public`, meaning any variable can access them freely.  However, you can restrict which methods and attributes can be accessed.  There are two days to do so: one is making a method or attribute `protected`, which means only classes and subclasses can access them.  If a method or attribute is `private`, then only the class itself can access it from within; it can't be accessed elsewhere.

Here is an example:
```ts
class Dwelling {
    address: string;
    bedrooms: number;
    bathrooms: number;
    floors: number;
    protected color: string;
    private owner: string;

    constructor(address: string, bedrooms: number, bathrooms: number = 1, floors: number = 1) {
        this.address = address;
        this.bedrooms = bedrooms;
        this.bathrooms = bathrooms;
        this.floors = floors;
        this.color = "Blue";
        this.owner = "me";
    }

    clean() {
        console.log("Cleaning dwelling place");
    }
}

class House extends Dwelling { // Inheriting from the Dwelling (parent) class; the House (child) class is a *subclass* of the Dwelling class
    hasBackyard: boolean;

    constructor(address: string, bedrooms: number, bathrooms: number = 1, floors: number = 1, hasBackyard: boolean = true) {
        super(address, bedrooms, bathrooms, floors); // Call on constructor from Dwelling class
        this.hasBackyard = hasBackyard;
    }

    clean() {
        super.clean(); // Call on Dwelling's clean method
        console.log("Phew - all done!");
    }

    getColor() {
        console.log(`The house is ${this.color}.`); // OKAY as color is a protected attribute, and a subclass can access it
    }

    getOwner() {
        console.log(`The owner is ${this.owner}.`); // ILLEGAL: owner is private, so we can't access it in this subclass
    }
}

let myHouse: House = new House("415 Meridian St.", 3, 2);
console.log(myHouse.color); // ILLEGAL: protected (and private) attributes can't be accessed here
```
Note that interfaces require that attributes be made public.

## Inheriting from interfaces
You not only have the ability to inherit from classes, you can also in TypeScript inherit from interfaces!  You can actually even inherit from multiple interfaces if you wish.

Here's how you can have a class inherit - or *implement* - an interface:
```ts
interface Building {
    rooms: number; // Attributes cannot be private or protected
    floors: number;
    repaint(): void;
}

class House implements Building { // Notice the keyword "implements" here
    // Notice the word "public" here so we can instantiate the fields from the Building interface, which are also public
    constructor(public rooms: number, public floors: number) { 
        this.rooms = rooms;
        this.floors = floors;
    }

    repaint(): void { // Must implement (implicitly abstract) methods from interface!
        console.log("Repainting house!");
    }

    rebuild() {
        console.log("Demolishing and rebuilding the house!");
    }
}

class Warehouse implements Building {
    height: number;

    constructor (public rooms: number, public floors: number, height: number = 100) {
        this.rooms = rooms;
        this.floors = floors;
        this.height = height;
    }

    repaint(): void {
        console.log("Repainting entire warehouse");
    }

    rebuild() {
        console.log("Rebuilding warehouse!");
    }
}
```

Additionally, you can have an interface inherit another interface.  Here is an example:
```ts
interface Phone {
    number: string,
    brand: string,
    memory: number,
    turnOn: () => void
}

interface Android extends Phone { // Inheriting from an interface by "extend"ing it (you can inherit multiple interfaces)
    // We already bring in number, brand, memory and turnOn from the Phone interface
    version: string,
    appInterfaceType?: string // Optional key
}

const myAndroid: Android = {
    number: "555-123-4567",
    brand: "Samsung",
    memory: 1024,
    version: "12.3",
    turnOn: function(): void {
        console.log("Press the power button!");
    }
}
```
Notice how we use the word `extends` when an interface inherits from another interface vs. the word `implements` when a class inherits from an interface.  So an interface can extend another interface, an a class implements an interface.

## Abstract classes
An **abstract class** is a class that cannot be instantiated.  In other words, you cannot create an item that is of this class directly.  Think of it like a blueprint that other classes must implement.  Imagine a television set.  There are many features and abilities that it can have, like the ability to connect to the internet, use Bluetooth, HDR, etc.  There are many common features a TV can have, like a screen, a power button, etc.  But other features and abilities will depend on each TV, and some manufacturers will have their own ways of implementing stuff.  One manufacturer might support the newest WiFi standard, but another might not.  One might have several buttons on the TV itself, while other might have a single button that can handle many operations.  

Now that you have an idea of a real life example where an abstract class could be useful, let's demonstrate how in TypeScript:
```ts
abstract class Television {
    size: number;
    isOn: boolean;
    abstract turnOn(): void; // Abstract method
    constructor(size: number) {
        this.size = size;
        this.isOn = false;
    }
}

class MagicTV extends Television {
    brand: string;
    constructor(size: number, brand: string = "Magic") {
        super(size);
        this.brand = brand;
    }

    turnOn() { // You must implement abstract methods!
        this.isOn = true;
    }
}
```
In the `Television` abstract class, we have attributes `size` and `isOn`.  If you create attributes in an abstract class, they must be implemented in the constructor or otherwise assigned a default value.

If you add a constructor in an abstract class, then every subclass that implements it *must* pass in the same parameters at a bare minimum as the parameters in the abstract class's constructor as well.  In this example `size` is a parameter in the `Television` abstract class, and so we must do the same in the `MagicTV` class.

Abstract methods can be added as well.  Subclasses then must implement them, like the `turnOn` method in the example above.

JavaScript does not currently have an implementation for abstract classes.

## Abstract classes vs. interfaces vs. base (generic) classes
To be added!