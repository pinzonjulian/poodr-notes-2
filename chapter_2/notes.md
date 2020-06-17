# Chapter 2: Designing Classes with a Single Responsibility

These are some questions posed by Sandi at the beginning of this chapter when talking about the overwhelming feeling the produce when you're designing.

> What are your classes?
> How many should you have?

I'm starting to feel that I can have many more than I've been creating. There are many things that I can model as objects that I haven't in the past.

> What behavior will they implement?

Not sure yet 🤷🏽‍♂️

> How much do they know about other classes?

From previous knowledge, I'm guessing not much.

> How much of themselves should they expose?

Good question. I'm thinking this relates to private vs public methods. My current way of thinking about this is: Is this question (method) that I'm asking one I'd ask outside of the context of this class? If it is, it's public, if not, it's private. Seems a bit obvious but 🤷🏽‍♂️

> Creating an easy-to-change application, however, is a different matter. Your appli- cation needs to work right now just once; it must be easy to change forever.

## 2.1 Deciding What Belongs in a Class

### 2.1.1 Grouping Methods into Classses

> They [classes] define a virtual world, one that constrains the imagination of everyone downstream

> Design is more the art of preserving changeability than it is the act of achieving perfection.

### 2.1.2 Organizing Code to Allow for Easy Changes

\* The following excerpt comes directly from the book:

---

Easy to change code can be defined as:
- Chages have no unexpected side effects
- Small changes in requirements require correspondingly small changes in code
- Existing code is easy to reuse
- The easiest way to make a change is to add code that in itself is easy to change

The code you write should have the following qualities. The code should be:
- _Transparent_

  The consequences of change should be obvious in the code that is chaning and in distand code that relies upon it

- _Reasonable_

  The cost of any change should be proportional to the benefits the change achieves

- _Usable_

  Existing code should be usable in new and unexpected contexts

- Exemplary

  The code itself should encourage those who change it to perpetuate these qualities

---

## 2.2 Creating classes that have a single responsibility

### 2.2.1 An example application: bicycles and gears
Has some code examples. Refer to the book for them :)

### 2.2.2 Why single responsibility matters
> An application that is easy to change is like a box of building blocks; you can select just the pieces you need and assemble them in unanticipated ways.

Sandi compares an app to a *box of building blocks* with *blocks* being objects. I like this metaphor because it talks about the idea of very simple generic objects like the 2x2 lego piece or the 2x8 one. Lego blocks have an interface that makes them pluggable (the circular bumps and holes).

In contrast, I can imagine the oposite example being a puzzle with different colors and unique plugs between one piece and the other. Puzzle pieces are not interchangeable. OO code, for the most part, should maleable.

I can't help to think about DHH's talk (2018 rails' keynote maybe) where he talks about developers wanting to build the millennium falcon with ultra-basic lego blocks. He mocks that idea by putting that multicolored, semi-faithful rendition of the ship alongside the hi-fi one.

Trying to put these two concepts together might seem a bit hard but I think it's all about context. When building a Rails app, you need to be mindful of which times you should use the barebones lego pieces and where you might be better off using the hi-fi ones. I think the trick lies there when building apps with OSS like Rails and all of the community building gems.

> A class that has more than one responsibility is difficult to reuse

Definitely. I think the biggest problem I've faced is building classes that have more than one responsibility. Take as an example (probably not the best onen but I'm creating it as I go) a Rails model called `JobPost`:

| Job Post                | type    |
|---                      |  ---    |
| `title`                 | `string`|
| `description`           | `text`  |
| `address_body`          | `string`|
| `address_latitude`      | `float` |
| `address_longitude`     | `float` |

This model has 2 responsibilities and they're easy to see by looking at the `address_` prefix on some of the fields. This model has a hidden `address` or `address` model within itself. If you wanted to reuse this class to provide the user with a template for job post that includes only `title` and `description` you're going to have a problem.

> If the responsibilities are so coupled that you cannot use just the behavior you need, you could duplicate the code of interest. This is a terrible idea.

Imagine this duplication being a new class `JobTemplate`:

| Job Template            | type    |
|---                      |  ---    |
| `title`                 | `string`|
| `description`           | `text`  |

> Because the class you’re reusing is confused about what it does and contains several tangled-up responsibilities, it has many reasons to change.

*It has many reasons to change*

I find this is a very nice way of telling if your class is well built and isolated. In general your classes shouldn't have many reasons to change. At least not at a core level of what defines them.

### 2.2.3 Determining if a class has a single responsibility

#### Method #1: The question method

Sandi has another method to determine if classes have a single responsibility. In my example I could ask the following questions:

1. ✅ _Mr. JobPost, what's your title?_
2. ✅ _Mr. JobPost, what's your description?_
3. 😱 _Mr. JobPost, what's your latitude?_
3. 😱 _Mr. JobPost, what's your latitude?_

It's weird to ask `JobPost` about latitude right?
However, Sandi says if it's a question that `JobPost` answers, someone will ask it. This is where things get shady; when you create a canned response to a question that shouldn't even be asked.

#### Method #2: No "and" or "or" in your one-sentence description, s'il vous plait! 🇫🇷🥖

In my example:

🚨_"The `JobPost` class's responsibility is to describe what the job's about AND where it is located"_

If it uses the word `AND` it has more than one responsibility.

If it uses the word `OR`, that other responsibility might not even be related

### The concept of cohesion

For a class to be highly cohesive, everything in it should relate to its _central purpose_. This can also be phrased as having a _Single Responsibility_.

> SRP doesn’t require that a class do only one very narrow thing or that it change for only a single nitpicky reason; instead SRP requires that a class be cohesive—that everything the class does be highly related to its purpose.

### 2.2.4 Determining when to make design decisions.

> “What is the future cost of doing nothing today?”

If a class like `Gear` (refer to the book) were to start depending on other classes or vice-versa, that's a good time to start thinking about cleaning it up, creating new objects and better abstractions. However, at this point, `Gear` is all alone in the world and only I know about it. It's not worth to make any further abstractions at this point.

> 👍🏽 Rule of thumb: When the future cost of doing nothing is the same as the current cost, postpone the decision. Make the decision only when you must with the information you have at that time.

However, when working in a team, or even by yourself, setting the correct example might be the best course of action:

> The structure of every class is a message to future maintainers of the application. It reveals your design intentions. For better or for worse, the patterns you establish today will be replicated forever.

Patterns, specially bad patterns, extremely good at at mitosis. They will replicate _very_ fast.

> 👍🏽 Rule of thumb: Make your code exemplary

Even though I make my greatest effor to make exemplary code,sometimes you just don't have enough information or time to make the best decision. In those cases, even leaving a comment might be a good idea. Leave a comment in the PR and/or in the code itself to convey your intention and the reason behind your design decisions. For example:

```ruby
  class JobPost
    # ⚠️ Be warned! Latitude should not live in there in the future. It was placed here because of X, Y and Z but it will be refactored.
    attr_accessor :latitude
  end
```

## 2.3 Writing code that embraces change


### 2.3.1 Depend on behavior, not data || Techniques to embrace change

##### Hide instance variables
> Always wrap instance variables in accessor methods instead of directly referring to variables.

##### Hide Data structures
