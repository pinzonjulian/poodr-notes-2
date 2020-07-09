# Chapter 6: Aquiring behavior through inheritance

## 6.1 Understandin Classical Inheritance

_Automatic message delegation_ is when you define a path for not-understood messages. If the object doesn't respond to a message, there is a path higher up that searches for that message and executes it if it's found. With inheritance this behavior comes automatically.

## 6.2 Recognizing where to use inheritance

### 6.2.1 Starting with a Concrete Class

```ruby
class Bicycle
  attr_reader :size, :tape_color
  def initialize(**opts)
    @size = opts[:size] @tape_color = opts[:tape_color]
  end
  # every bike has the same defaults for
  # tire and chain size
  def
    { chain: '11-speed'
      tire_size: '23',
      tape_color: tape_color }
  end
  # Many other methods...
end

bike = Bicycle.new( size: 'M', tape_color: 'red')
puts bike.size # => M
puts bike.spares
# => {:chain=>"11-speed", :tire_size=>"23", :tape_color=>"red"}
```

### 6.2.2 Embedding multiple types

> This code contains an if statement that checks an attribute that **holds the category of self** to determine what message to send to self. This should bring back memories of a pattern discussed in the previous chapter on duck typing, where you saw an if statement that checked the class of an object to determine what message to send to that object.

> In both of these patterns, an object decides what message to send based on a category of the receiver. You can think of the class of an object as merely a specific case of an attribute that holds the category of self; considered this way, these patterns are the same. In each case, if the sender could talk, it would be saying, “I know who you are, and because of that, I know what you do.” This knowledge is a dependency that raises the cost of change.

Checking for the "type" of something inside of a class (e.g one of its attributes) might be an indicator of a hidden *subclass* in your code.

### 6.2.3 Finding the embedded types

> This is the exact problem that inheritance solves: that of highly related types that share common behavior but differ along some dimension.

### 6.2.4 Choosing inheritance
In Ruby there is only "single inheritance" where a subclass is only allowed one parent superclass.

In ruby if you don't define the parent for a class, it automatically becomes a subclass of `Object`.

> The fact that unknown messages get delegated up the superclass hierarchy implies that subclasses are everything their superclasses are, plus more.

### 6.2.5 Drawing inheritance relationships

## 6.3 Misapplying Inheritance
## 6.4 Finding the abstraction

> Subclasses are specializations of their superclasses. A `MountainBike` should be everything a `Bicycle` is, **plus more**

1. The objects that you are modeling must truly have a generalization-specialization relationship
2. You must use the correct coding techniques


### 6.4.1 Creating an Abstract Superclass

Bicycle now represents an **abstract class**. This new version of bicycle doesn't define the an entire bicycle but bits of traits that **all of them share**.

Some languages, like Java, have keywords that prevent abstract classes from being instantiated. Ruby does not.

However Rails does! For classes that inherit from `ApplicationRecord` you can define `self.abstract_class = true` inside of the class to tell Rails this is an abstract class.

> Abstract classes exist to be subclassed. This is their sole purpose.

Some advice from Sandi:
> Even though you now have a requirement for two kinds of bikes, this still may not be the right moment to commit to inheritance. Creating a hierarchy has costs; the best way to minimize these costs is to maximize your chance of getting the abstrac- tion right before allowing subclasses to depend on it

### 6.4.2 Promoting Abstract Behavior
Advice on refactoring:
The size method began in the bloated `Bicycle` class. We then put everything inside `Bicycle` in `MountainBike`. Afterwards, we moved back into `Bicycle` the methods that would be common to both bikes. This is what Sandi calls the
*push-everything-down-and-then-pull-some-things-up strategy*

> Many of the difficulties of inherit- ance are caused by a failure to rigorously separate the concrete from the abstract.

We're again presented with the challenge of recognizing what is concrete and what is abstract.

By moving everything into `MountainBike` we allow ourselves to not miss any of the *concrete* aspects of a mountain bike behind. We end up pushing abstractions up rather than pulling concretions down.

> When a bit of the abstraction gets left behind, the oversight becomes visible as soon as another subclass needs the same behavior. In order to give all sub- classes access to the behavior, you’ll be forced to either duplicate the code (in each subclass) or promote it (to the common superclass).

> The general rule for refactoring into a new inheritance hierarchy is to arrange code so that you can promote abstractions rather than demote concretions.

### 6.4.3 Separating Abstract from Concrete
See examples in the book

### 6.4.4 Using the template method pattern
```ruby
class Bicycle
  attr_reader :size, :chain, :tire_size
  def initialize(**opts)
    @size = opts[:size]
    @chain = opts[:chain] || default_chain
    @tire_size = opts[:tire_size] || default_tire_size
  end

  def default_chain
    "11-speed" # <- common default
  end
  # ...
end

class RoadBike < Bicycle # ...
  def default_tire_size  # <- subclass default
    "23"
  end
end

class MountainBike < Bicycle # ...
  def default_tire_size # <- subclass default
    "2.1"
  end
end
```

This is a very interesting pattern I hadn't seen. When `Bicycle` calls `default_chain` in the intialize method it would seem as if it were calling a method on the child. However, the way I wrapped my head around this is the following. When `MountainBike` inherits from `Bicycle` it sort of *becomes* bicycle. Now when I initialize `MountainBike` that instance knows about `@chain = opts[:chain] || default_chain`. The parent defines that procedure for initialization but it's owned by the children.

> Sub- classes contribute to the algorithm by implementing matching methods.

### 6.4.5 Implementing Every template method

When new children of bicycle are created, they'll encounter an error because `default_tire_size` is not defined.

If you're the designed of `Bicycle` implement the following method to allow new users to know that they have a task to do.

```ruby
class Bicycle
  # ...
  def default_tire_size
    raise NotImplementedError, "#{self.class} should have implemented ..."
  end
end
```

> 💡 Always document template method requirements by implementing matching methods that raise useful errors.

## 6.5 Managing Coupling between Superclasses and Subclasses

```ruby
class RoadBike < Bicycle
  attr_reader :tape_color
  def initialize(**opts)
    @tape_color = opts[:tape_color]
    super
  end
  def spares
    super.merge(tape_color: tape_color)
  end
  #...
end
```
Take a look at the calls to `super`. This is a dependency the children have on the parent. Programers may forget to add super when creating new classes resulting in wierd behaviors in the children.

> The pattern of code in this hierarchy requires that subclasses not only know what they do but also how they are supposed to interact with their superclass.

> When a subclass sends super, it’s effectively declaring that it knows the algorithm; it depends on this knowledge.

# 6.5.2 Decoupling Subclasses using hook messages

`Hooks` remove knowledge of the algorithm from the subclass and returns control to the superclass.

```ruby
class Bicycle
  attr_reader :size, :chain, :tire_size
  def initialize(**opts)
    @size = opts[:size]
    @chain = opts[:chain] || default_chain
    @tire_size = opts[:tire_size] || default_tire_size
    post_initialize(opts) # Bicycle both sends end
  end
  def post_initialize; end
end
```

```ruby
class RoadBike < Bicycle
  attr_reader :tape_color
  def post_initialize(opts)
    @tape_color = opts[:tape_color]
  end
end
```

Notice that now `RoadBike` doesn't even have an initialize method. It `hook`s into initialize via the `post_initialize` method and no longer controls initialization; it contributes to it.
