# Chapter 1
> In a world of objects, new arrangements of behavior emerge naturally. You don’t have to explicitly write code for the spouse_steps_on_cat procedure; all you need is a spouse object that takes steps and a cat object that does not like being stepped on.

> [this book] views the world as a series of spontaneous interactions between objects.

I love these two quotes to explain OOP.

### 1.1.1 The problem design solves
> Change is unavoidable. It is ubiquitous, omnipresent, and inevitable.

> Applications that are easy to change are a pleasure to write and a joy to extend. They’re flexible and adaptable. Applications that resist change are just the opposite; every change is expensive and each makes the next cost more.

### 1.1.2 Why change is hard
> Getting the right message to the correct target objet rquired that the sender of the message know things about the receiver.

OOD is about _managing dependencies_, about the techniques that allow objects to tolerate change by arranging their dependencies.

> In the absence of design, unmanaged dependencies wreak havoc because objects know too much about one another.

It seems that the real challenge is for objects having just enough knowledge about each other to interact but not too much to make change hard.

> When objects know too much, they have many expectations about the world in which they reside. They’re picky, they need things to be “just so.” These expectations constrain them. The objects resist being reused in different contexts; they are painful to test and susceptible to being duplicated.

###  1.1.3 A practical definition of design

The way we arrange the code is what Sandy defines as "design". That's where programmers exercise their creativity.

> Design is thus an art, the art of arranging code.

Designing an application well means designing for the present and the future. Future in the sense that you will have to go back and most likely change your design; change the way you organised your code.

> Practical design does not anticipate what will happen to your application; it merely accepts that something will and that, in the present, you cannot know what. It doesn’t guess the future; it preserves your options for accommodating the future. It doesn’t choose; it leaves you room to move.

## 1.2 The tools of design

### 1.2.1 Design principles
SOLID:
```
S: Single responsibility
O: Open closed
L: Liskov Substitution
I: Interface segregation
D: Dependency inversion
```
Other principles:
- DRY: Don't repeat yourself
- LoD: Law of Demeter

Key takeaway: NASA does OOP and it seems to work for them 😂

### 1.2.2 Design Patterns
Key takeaway: Don't do like the juniors. Learn you patterns but apply them correctly. Don't use patterns meant to solve problem A on problem B.

> 😔This book's not about patterns but should prepare me to study them 💪🏽

## 1.3 The Act of Design

> However, successful but undesigned applications carry the seeds of their own destruction; they are easy to write but gradually become impossible to change

> Slightly more experienced programmers encounter different design failures. These programmers are aware of OOD techniques but do not yet understand how to apply them. With the best of intentions, these programmers fall into the trap of overdesign

> 👆🏽Are you talking about me Sandy?

> In an excess of enthusiasm, they apply principles inappropriately and see patterns where none exist

Now I'm scared. I've felt recently that I'm starting to see more clearly where a new class should be created or where a new pattern has emerged. Have I been tricked?

### 1.3.3 Judging Design
Left blank

## 1.4 A brief introduction to Object-Oriented Programming
Left blank
