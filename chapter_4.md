# Chapter 4: Creating flexible interfaces

> an object-oriented application [...] is made up of classes but efined by messages

|                              |                  |
|------------------------------|------------------|
| What objects know            | responsibilities |
| Who objects know             | dependences      |
| How they talk to one another | interfaces       |

## 4.1 Understanding interfaces
Difficult to use objects ****expose too much of themselves**** and **know too much about their neighbors**.

In contrast, easy to use objects **reveal very little about themselves** and **know very little about others**. These objects have a very defined and constrained set of methods that it expects others to use. This is the object's _public interface_.

## Defining interfaces
_The kitchen analogy_

Sandi explains that in a restaurant, the _public interface_ the customer interacts with to get food from the kitchen is **the menu**. The user doesn't need to understand _how_ the kitchen prepares the food, nor does it have permission to go in a do stuff by themselves.

> 👨🏽‍🍳 In OOP, the class is like the kitchen.

### 4.2.1 Public interfaces

Public methods should:
- Reveal the object's primary responsibility
- Are expected to be invoked by others
- Will not change on a whim
- Are safe for others to depend on
- Are thoroughly documented in the tests

### 4.2.2 Private interfaces

These methods:
- Handle implementation details
- Are not expected to be sent by other objects
- Can change for any reason whatsoever
- Are usnafe for others to depend on
- May not even be references in the tests

# 4.2.3 Responsibilities, Dependencies and Interfaces

A Class' responsibility is to fulfill a single purpose. That purpose is met with some smaller specific responsibilities (public methods).

The public parts of a class are the stable parts -> these are the ones that are exposed to others making them more crucial and harder to change since they define a dependency.

> When your classes use the public methods of others, you trust those methods to be stable.

## 4.3 Finding the Public Interface

### 4.3.1 An example application: Bicycle Touring Company
Domain objects: Classes that represent the nouns in the applications that have both data and behavior.

> These are obvious because they're persistent. They stand for big visible real-world things that will end up with a representation in your database

> 😱 This is very true. I hadn't thought of those nouns as the obvious domain models that first pop into mind.

### 4.3.3 Using sequence diagrams
This is a short numeral and has great insight into thinking about messages by drawing sequence diagrams. Go read it again :)

### 4.3.4 Asking for "What" instead of telling "How"
This is a short numeral and has great insight into thinking about messages by drawing sequence diagrams. Go read it again :)

### 4.3.5 Seeking Context independence
> The context that an object expects has a direct effect on how difficult it is to reuse. Objects that have a simple context are easy to use and easy to test; they expect few things from their surroundings. Objects that have a complicated context are hard to use and hard to test; they require complicated setup before they can do anything

The following is my intepretation of the use case with the information given.
```ruby
class Trip
  attr_reader :mechanic, :bicycles
  def initialize(mechanic:)
    @mechanic = mechanic
  end

  def prepare
    @bicycles = mechanic.prepare_trip(self) #weird assignement
    bicycles.each do |bike|
      mechanic.prepare_bycicle(bike)
    end
  end
end

trip = Trip.new(mechanic: mechanic)
trip.prepare
```
I'm not very sure about this intepretation of the diagram.

### 4.3.6 Trusting other objects
Read again to see the differences between all of the examples of trip preparation.

### 4.3.7 Using messages to discover objects
Think about the message first!

## 4.4 Writing code that puts it best (inter)face forward

_Rules of thumb for creating interfaces_

### 4.4.1 Create explicit interfaces

> Everytime you create a class, declare its interfaces. Methods in the public interface should:

- Be explicitly defined as such
- Be more about _what_ than _how_
- Have names that, insofar as hou can anticipate, will not change
- Prefer keyword arguments

Makre your public interface **inescapably obvious**
||||
|-|-|-|
| private   | least stable | most restrictive visibility |
| protected | unstable     | visible as long as: receiver is self or an instance of the same class or subclass of self |
|pubic      | most stable  | visible EVERYWHERE           |

### 4.4.2 Honor the public intrfaces of others
...
### 4.4.3 Exercise caution when depending on private interfaces
If you need to depend on a private method, **isolate it!** (see chapter 3)

### 4.4.4 Minimize Context
> [...] create public methods that allow senders to get what they want without knowing how.

Minimize the context your class requires from other classes. Focus on what not how!

## 4.5 The law of demeter
### 4.5.1 Definig Demeter
Do notsend a message to a third object via a second objet of a different type.

-----------------------------------------------
- Only talk to your immediate neighbors. Or
- Use only one dot!
-----------------------------------------------

Bad examples!
- `customer.bicycle.wheel.tire`
- `customer.bicycle.wheel.rotate`

Example that looks bad but it's actually ok
- `hash.keys.sort.join(',')`

### 4.5.3 Avoiding Violations
#### Using delgation
> to delegate a message is to pass it on to another object, often via a **wrapper method**

> 🚨 This technique is sometimes useful, but beware: It can result in code that obeys the letter of the law while ignoring its spirit. Using delegation to hide tight coupling is not the same as decoupling the code.

### 4.5.4 Listening to Demeter

`customer.bicycle.wheel.tire` and  `customer.bicycle.wheel.rotate` become `customer.ride` where `ride` hides all the implementation that `Trip` needs.

> The trainwrecks of Demeter violations are **clues that there are objects whose public interfaces are lacking**.

## 4.6 Summary
MESSAGE - MESSAGE - MESSAGE!

Shift your view from your easily findable domain objects into the messages you want to send between objects. This defines your public interface.

Make sure to look for hidden objects in the messages you want to send.
