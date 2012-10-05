
<div id="#title"><img src="title.png"></div>
\includegraphics[width=120px]{title.png}

*Philly Coders JVM Meeting*\
*6 June, 2012*\
*Philadelphia PA*

----------

**Brian Clapper**\
ArdenTex, Inc.\
Founder: Philly Area Scala Enthusiasts (PHASE)

*@brianclapper*\
*bmc@ardentex.com*

----------

# Scala

* Scala is a general purpose programming language that compiles down to JVM 
  byte code. Just like Java.
* Scala is not a scripting language; it is compile-time type-safe. Just like 
  Java—only more so.
* Scala is more concise and more powerful than Java.
* Scala is interoperable with Java.
* Performance is generally on par with Java.
* Tool support is improving, but still not as robust as with Java.

----------

# Features

* functional and object-oriented support (conceptually akin to OCaml and F#)
* type inference, allowing simpler syntax without sacrificing compile-time 
  type safety
* closures
* language syntax inference
* all expressions return values (like Ruby or CoffeeScript)
* the ability to extend third-party APIs with programmable type conversion
  (the "pimp my library" pattern)
* powerful pattern matching
* a REPL (console)
* and much more

----------

# Everything's an object

* Scala has no primitives. Java’s primitives are intelligently auto-boxed and 
  unboxed. An integer is always Int and always an object.
* Functions are also objects.
* Methods are auto-coerced into functions, where necessary.

----------

# Type inference

The Scala compiler can often infer the type of a variable or function result:

```scala
val s = "foo"  // identical to: val s: String = "foo"
val i = 10     // Int

def transform(i: Int) = i.toString() // returns String
```

This feature makes Scala almost as tersely expressive as languages like Ruby or Python.

----------

# Inheritance

Scala, like Java, supports single inheritance only.

However...

Scala’s equivalent of Java’s interfaces, called traits, allow code, not just
function declarations and constants.

Scala’s traits are roughly analogous to Ruby’s modules.

Traits, like modules, turn out to be a powerful way to compose systems, without
all the mayhem of actual multiple inheritance.

----------

# Functions

Here's a function to compute the sum of two numbers:

```scala
def sum(a: Int, b: Int): Int = {
  a + b
}
```

Note the lack of a return statement.

----------

# Functions

Since the function has only one statement, it can be simplified:

```scala
def sum(a: Int, b: Int): Int = a + b
```

But we're not done yet...

----------

# Functions

Since **a** and **b** both have type `Int`, the Scala compiler can infer that the return value is `Int`, so we can leave the return type off the declaration:

```scala
def sum(a: Int, b: Int) = a + b
```

----------

# Functions

Notice the lack of semicolons. You can omit them, as long as the compiler isn't confused. For instance:

```scala
def factorial(n: Int): Int = {
  if (n == 0)
    1
  else
    n * factorial(n – 1)
}
```

----------

# Immutability

Scala supports immutability, a key concept in functional programming.

* The _val_ keyword denotes an immutable variable
* Scala’s powerful for loop (really, syntactic sugar over functional 
  transformations) means you never need to increment a loop control variable.
* The compiler does tail-call optimization, to allow loops to be expressed as 
  recursive calls, without a loss of efficiency.

----------

# Immutability

An immutable function, with tail-call optimization:

```scala
/** Convert a file into a path array. Borrowed from SBT
  * source code.
  */
private def toPathArray(file: File): Array[String] = {
  @tailrec
  def toPathList(f: File, cur: List[String]): List[String] = {
    if (f == null)
      cur
    else
      toPathList(f.getParentFile, f.getName :: cur)
  }

  toPathList(file.getCanonicalFile, Nil).toArray
}
```

Scala compiles this whole thing down to a loop.

----------

# Immutability

A for loop that loops over an array:

```scala
for (i <- 0 until a.length)
  println(a[i])
```

A better version:

```scala
for (elem <- a)
  println(elem)
```

This is syntactic sugar for:

```scala
a.foreach { println(_) }
```


----------

# Lambdas

What is this, really?

```scala
a.foreach { println(_) }
```

Let’s define one of our own:

```scala
def myFor[T](it: Iterable[T], func: T => Unit) = {
  for (elem <- it)
    func(elem)
```

`myFor` is a function that takes an iterable and other function. It traverses the iterable and calls the supplied function with each element.

```scala
  myFor(a, println)
```

----------

# Lambdas

We can do better, though:

```scala
def myFor[T](it: Iterable[T])(func: T => Unit) = {
  for (elem <- it)
    func(elem)
```

By moving the function parameter into a second parameter list, we can now omit the parentheses and pass a function literal:

```scala
myFor(a) {println _}
```

Voilà! We just made our own control structure! (Kind of...)

The Scala for loop is exactly this kind of syntactic sugar.

----------

# Lambdas

Of course, we’re not limited to just `println`:

```scala
val a = (1 to 20).map {_ * 2}.toArray
myFor(a) { i: Int =>
  doSomethingWithInt(i)
}

def printAsFloat(i: Int) = println i.toFloat
myFor (a) { printAsFloat _ }
```

Any function or functional literal that matches the expected type is permitted.

----------

# Objects

* There are no static methods in Scala
* Instead, you use special singleton objects
* These objects can also be paired with classes (so-called companion objects), 
  allowing them to do special things
* These singleton objects can encapsulate common methods
* They also act as containers for implicit conversion functions (more on that 
  later)
* When imported, they look rather like Python-style modules

----------

# Pattern matching

Here's a (silly) function to map a number to a letter:

```java
public boolean letterFor(int number) { // Java
    switch (number) {
        case 1:  return "a"
        case 2:  return "b"
        case 3:  return "c"
        default: return "?"
    }
}
```

```scala
def letterFor(number:Int) = { // Scala
  number match {
    case 1 => "a"
    case 2 => "b"
    case 3 => "c"
    case _ => "?"
  }
}
```

But isn't that just a *switch* statement?

----------

# Pattern matching

No. It’s much more useful. Especially when you mix extractors into the picture.

```scala
class Color(val red:Int, val green:Int, val blue:Int)
 
case class Red(r:Int) extends Color(r, 0, 0)
case class Green(g:Int) extends Color(0, g, 0)
case class Blue(b:Int) extends Color(0, 0, b)
 
def printColor(c:Color) = c match {
  case Red(v) => println("Red: " + v)
  case Green(v) => println("Green: " + v)
  case Blue(v) => println("Blue: " + v)
 
  case col:Color => { // some other color
    printrf("RGB: #%02d%02d%02d", col.red, col.green, col.blue)
  }
 
  case null => println("Invalid color")
}
```

----------

# Pattern matching

Pattern matching can be used in a variety of ways

Here's a pattern match that discards the first element of a list:

```scala
    def dropOne[T](l: List[T]): List[T] =
      l match {
        case Nil => Nil
        case item :: sublist => sublist
      }
```

The :: is the list _cons_ operator, very similar conceptually to _cons_
in Lisp.

Pattern matching can get even more complicated than that.

----------

# Pimp my library

Scala lets you add functions to existing, read-only libraries, sort of like C# extensions.

When the compiler sees a call to an undefined method, it looks for in-scope converter methods that can convert the calling object into something that does have the right method.

It allows you to do things like this:

```scala
val s = "Foobar" // String (java.lang.String)

println(s(0)) // "F"
println(s(3)) // "b"
```

The subscript _s(0)_ is really a call to a Scala method that lives in an entirely different class from `java.lang.String`.

----------

# DSLs

As with languages like Ruby, these flexible language constructs make internal DSLs trivial to build in Scala. DSL techniques are used, among other places:

* to simplify use of Java’s Swing UI components
* to provide parser combinator libraries
* to create expressive syntaxes for actor libraries
* etc.

----------

# And... I'm out of time

This presentation only scratches the surface of what’s possible with Scala. There’s much, much more.

----------

# And... I'm out of time

This presentation only scratches the surface of what’s possible with Scala. There’s much, much more.

If you're interested, let's talk.

----------

# And... I'm out of time

This presentation only scratches the surface of what’s possible with Scala. There’s much, much more.

If you're interested, let's talk.

Preferably, over a beer.
