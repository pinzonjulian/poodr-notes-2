# Chapter 3: Managing dependencies

> A single object cannot know everything, so inevitably it will have to talk to another object.

Behavior is either known by:
1. the objects themselves
2. their parent objects
3. the objects through some other object they know

> To collaborate, an object must know something about others. Knowing creates a dependency. If not managed carefully, these dependencies will strangle your application.

### 3.1.1 Recognizing dependencies

Ways to know if an object has a dependency:

1. It knows the name of some other class. For example, `Gear` needs to know that `Wheel` is a thing.
2. It knows the name of a message that it will send to someone else other than itself (`self`).
3. It knows about the argument a message requires. `Gear` knows that `Wheel.new` requires a `rim` and a `tire``
4. It knows the order of those arguments.

This is not to say that `Gear` will not depend on `Wheel` AT ALL. It's inevitable to have some degree of dependency because the two objects must collaborate together.

### 3.1.2 Coupling between objects (CBO)
Left blank
### 3.1.3 Other dependencies
Left blank

## 3.2 Writing loosely coupled code

## Techniques to reduce dependencies

### 3.2.1 Inject dependencies
```ruby
class Gear
  # ...
  def gear_inches
    ratio * Wheel.new(rim, tire).diameter
  end
end
```
This piece of code creates a problem where `Gear` can only calculate `gear_inches` for `Wheel` and nothing else.
> `Gear` refuses to collaborate with any other kind of object, even if that object has a diamater and uses gears.

By injecting `Wheel` as a dependency, we can get around this issue.
```ruby
class Gear
  def initialize(chainring, cog, wheel)
    @wheel = wheel
  end

  def gear_inches
    ratio * wheel.diameter
  end
end
```

Now we can pass anything to Gear as long as it responds to the method `diameter`

### 3.2.2 Isolate dependencies
> Think of every dependency as an alien bacterium that’s trying to infect your class. Give your class a vigorous immune system; quarantine each dependency. Dependen- cies are foreign invaders that represent vulnerabilities, and they should be concise, explicit, and isolated.

If for some reason we're not able to inject `wheel` as a dependency, we might as well isolate the internal dependency as much as possible

```ruby
class Gear
  attr_reader :chainring, :cog, :rim, :tire

  def initialize(chainring, cog, rim, tire)
    # ...
  end

  def wheel
    @wheel ||= Wheel.new(rim, tire)
  end
end
```
Although `Gear` still knows too much (it takes in `rim` and `tire`) at least they're completely isolated. This technique exposes the dependency publicly instead of burying it inside some obscured method.

#### Isolate vulnerable external messages

In this example, the external message is `diameter` called on `wheel` as `wheel.diameter`. If for some reason `gear_inches` is a very complex method with multiple lines, the dependency of `wheel.diameter` will be concealed and hard to catch and maintain. We can isolate it in its own method inside of `Gear` like so:

```ruby
class Gear
  # ...
  def diameter
    wheel.diameter
  end
end
```

Having `wheel.diameter` is a dangerous dependency to have lying around. It's couples to the `wheel` class and to one of its methods `diameter`. It's best to isolate it from the start instead of waiting to DRY it out when multiple references are created within `Gear`.

### 3.2.3 Remove argument-order dependencies
> 💡 Keyword arguments FTW!

```ruby
class Gear
  # Change this
  def initialize(chainring, cog, wheel)
  end

  # To This
  def initialize(chainring:, cog:, wheel:)
  end
end
```
#### Explicitly define defaults
> 💡 Keyword arguments, different to hashes, allow you to add defaults

Adding complex defaults
```ruby
class Gear
  attr_reader :chainring, :cog, :wheel
  def initialize(chainring: default_chainring, cog: 18, wheel:)
  def initialize
    @chainring = chainring
    @cog = cog
    @wheel = wheel
  end

  def default_chainring
    (100/2) - 10 # or some, far more complex piece of logic
  end
end
```

#### Isolate multiparameter initialization

> 💡Wrap external interfaces (which you don't have control of) in your own modules

> The classes in your application should depend on code that you own; use a wrapping method to isolate external dependencies.

By using a module instead of a class, you're saying that you don't mean to have instances of `GearWrapper`. This is a factory!

## 3.3 Managing dependency direction
See book for example.

The gist of it is that you can define the direction of dependencies in both ways. `Gear` can know about `wheel` and its `diameter` or `Wheel` could know about `gear` and its method `gear_inches`. It's important to nail the direction to have a healthy app. (Don't know how yet though 🤷🏽‍♂️)

> ❓ How do you know you've picked the right direction for the dependencies?

### 3.3.2 Choosing dependency direction
Advice for classes:
> Depend on things that change less often than you do.

#### Understanding likelyhood of change

Ruby and its own classes are not very likely to change. As a developer, you wouldn't expect `String`, `Array` or the `*` operator to change dramatically. That makes depending on Ruby itself a good decision. Those objects will most likely change less often than your objects.

Frameworks are a little less stable than Ruby itself. For example, Rails is quite mature right now but there was a recent example where `secrets` were introduces and after a year they were deprecated in favor of `credentials`.

You need to be mindful of the rate of change of your framework of choice to decide if it's a good idea to use depend heavily on it or not. (Look at the history of JS framworks for example)

#### Recognizing concretions an abstractions
An example of concrete code; when `Gear` depended on `Wheel` and its methods `new` and `diameter`. When it was refactored with dependency injection, the dependency became much more abstract;`Gear` now depends solely on a method called `diameter` called on whatever object was passed at the `wheel` keyword argument.

#### Avoiding dependent-laden Classes
>  A class that, if changed, will cause changes to ripple through the application will be under enormous pressure to never change. Ever. Under any circumstances whatsoever. Your application may be permanently handicapped by your reluctance to pay the price required to make a change to this class.

#### Finding the dependencies that matter
