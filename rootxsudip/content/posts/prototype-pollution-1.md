---
title: "Demystifying Prototype Pollution for Beginners"
date: 2024-02-29T10:20:22+05:30
draft: false
toc: false
images:
tags:
  - Web Security
  - bug bounty
  - ctf 
---

Before diving into prototype pollution we need to learn about javascript objects and prototype basics.

### What is a javascript object?  
An object in a non-primitive data type which represents a collection of key value pairs. It is to used to store and organize data in a structure manner.

**Example code:**
```
//Object
let car = {
  name: "audi",
  model: "r8",
  engine: "V10"
}

//Accessing object properties
console.log(car.name)
console.log(car.model)
```

### Constructor:
Constructor functions are functions used as blueprints for creating multiple instances of objects with similar properties and methods.  
In the below code, we create two instances of car object and the car object act as blueprint.

### Prototype:
In JavaScript, the prototype is a mechanism that allows objects to inherit properties and methods from other objects. Every JavaScript object has a prototype, which is essentially a reference to another object from which it inherits properties. This inheritance mechanism is fundamental to JavaScript's object-oriented programming paradigm. 
* In simple language, when we create a object or function the js engine attaches a another object to it which have default properties,methods like toString() etc. That another object which is attach to our object is called prototype or __proto__

**Example Code:**
```
function Car(name,model,engine){
  this.name = name,
  this.model = model,
  this.engine = engine;
}

let car1 = new Car("Lamborghini","Revuelto","V12")
let car2 = new Car("Nissan","Z","VR30DDTT")

//Accessing object
console.log(car1)
console.log(car2)

``` 

#### Few key reasons why prototypes are used in javascript objects
1. Efficiet Memory Usage: When we create multiple instances of one object, each instance gets it's own memory. Prototype allows to define properties and methods once on the prototype object, which is shared among all the instances which reduces memory usage.
2. Inheritance: It facilliates inheritance in javascript. When a property or method is accessed on an object, if that property or method does not exist on the object itself, it tries to access the property from prototype chain upto global prototype
3. Dynamic Modification:  Prototypes can be modified dynamically at runtime. This means that we can add or modify properties and methods on the prototype of an object, and these changes will be reflected in all instances of that object.
4. Performance Optimization: Accessing properties and methods through prototypes can sometimes be faster than accessing them directly on the object itself.

**Example Code:**
```
function Car(name,model,engine){
  this.name = name,
  this.model = model,
  this.engine = engine;
}

//Adding a property in Car prototype
Car.prototype.category = function(){
  console.log("Sports")
}

let car1 = new Car("Lamborghini","Revuelto","V12")
let car2 = new Car("Nissan","Z","VR30DDTT")

//Accessing object properties
console.log(car1.category())
console.log(car2.category())
```
Here, car1 and car2 can access the method from their prototype.

#### Prototype Chain:
When a property or method is accessed on an object, JavaScript first looks for it directly on the object itself. If it's not found, JavaScript then follows the prototype chain, traversing through the linked prototype objects until it finds the property or method, or until it reaches the end of the chain (which is typically the Object.prototype).

Here's a breakdown of how the prototype chain works:
1. Object Creation: When an object is created using either object literals, constructor functions, or class syntax, it automatically inherits from a prototype.
2. Prototype Linkage: Every object (except for the root Object.prototype) has an internal [[Prototype]] property that references another object. This creates a chain of prototypes.
3. Property Lookup: When accessing a property or method on an object, JavaScript first checks if the property exists directly on the object. If not found, it follows the prototype chain, looking for the property or method in each linked prototype object.
4. Inheritance: Properties and methods defined in an object's prototype are inherited by all instances of that object. This promotes code reuse and allows for the implementation of inheritance patterns in JavaScript.
5. Modifying Prototypes Dynamically: Prototypes can be modified dynamically at runtime, allowing for changes to be reflected in all objects that inherit from them. This provides a powerful way to extend and customize the behavior of objects.

#### Prototype Pollution:
Prototype pollution is a vulnerability in JavaScript where an attacker can manipulate the prototype of objects to inject or modify properties and methods, potentially leading to unexpected behavior or security issues. This can occur when the application fails to properly validate and sanitize user input, allowing attackers to craft malicious payloads.
Example:
```
// Original object
let data = {
  name: "John",
  age: 30
};

// Malicious payload
let maliciousPayload = {
  __proto__: {
    isAdmin: true
  }
};

// Merge the original object with the malicious payload
Object.assign(data, maliciousPayload);

// Checking for admin status
console.log(data.isAdmin); // Outputs: true

```
1. We have an original object data with properties name and age.
2. An attacker crafts a malicious payload malicious Payload with __proto__ property pointing to an object with an isAdmin property set to true.
3. We then use Object.assign() to merge the original object data with the malicious payload. This results in the isAdmin property being added to the data object.
4. Now, when we check for the isAdmin property on data, it returns true, even though it wasn't originally part of the object. This demonstrates how the attacker was able to pollute the prototype chain and inject unauthorized properties.