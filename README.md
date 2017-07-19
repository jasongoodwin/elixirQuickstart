# elixir-fasttrack
Best way to get up and running is to write shit down. This is a tradition for me with new languages. Take a book, jot it down, reference it because I know what's here.

# iex
The REPL! Like basically every other language that matters, you have a REPL for interacting with the language.

There are a few useful ways to interact with Elixir.

## h

You can get help about a package with the `h` command

`h $module`

example:

``` elixir
ex(3)> h Map

                                      Map

A set of functions for working with maps.
[...]
```

Same for methods in a module:

`h $module.$function`

example:

```elixir
iex(4)> h Map.fetch

                              def fetch(map, key)

Fetches the value for a specific key in the given map.
[...]
```

# Data types

## Tuples

Not much to say here.

```
{val1, val2}
```

## Atom

Just a symbol constant

```
:ok
:"not ok"
```

You can see with pattern matching how this might be helpful:

```
#fail a method call
{:ok, result} = doStuff(failInput)
> false
#succeed a method call
{:ok, result} = doStuff(okInput)
> true
# result is now assigned
```

## Modules: 

somewhat equivalent to scala Objects (when used in the most functional sense). A useful abstraction for grouping related functionality together.

```
defmodule MeterToLengthConverter do
  defmodule Feet do
    def convert(m) do
      m * 3.28084
end end
  defmodule Inch do
    def convert(m) do
      m * 39.3701
    end
end end
```

Used like:

`MeterToLengthConverter.Feed.convert(8)`

It's clear to see how it's similar to the scala Object abstraction.

## Functions:

The `def` keyword defines functions

```
defmodule MeterToLengthConverter do
  defmodule Feet do
    def convert(m) do
      m * 3.28084
end end
  defmodule Inch do
    def convert(m) do
      m * 39.3701
    end
end end
```

You can overload the definitions. For example, using atoms you might do something like this to convert to different units:
  
```
defmodule MeterToLengthConverter do
  def convert(:feet, m), do: m * 3.28084
  def convert(:inch, m), do: m * 39.3701
  def convert(:yard, m), do: m * 1.09361
end
```

Matched from top down, as expected.

## Numbers

Numbers are pretty straight forward for a dynamic language - they'll adjust themselves to fit as needed. Just forget about worrying about it very much.

Functions exist in the default scope for dealing with numbers

```
iex> div(10,3) 3
iex> rem(10,3) 1
```

## Strings

Again, no suprises for a dynamically typed language.

```
iex(1)> "hey" 
"hey"
```

Strings are binary (sequences of bytes.) Char lists are different (eg List[Char] in scala isn't a string. But ByteString isn't a String either.) Strings in Elixir are more like Scala/Akka's ByteString.

## Maps

Standard KV pairs. Effectively O(1) I'm assuming.

```
iex> programmers = Map.new
%{}
iex> programmers = Map.put(programmers, :joe, "Erlang") 
%{joe: "Erlang"}
iex> programmers = Map.put(programmers, :matz, "Ruby") 
%{joe: "Erlang", matz: "Ruby"}
iex> programmers = Map.put(programmers, :rich, "Clojure") 
%{joe: "Erlang", matz: "Ruby", rich: "Clojure"}
```

This is a shit example because it re-assigns the variable reference - the underlying datastructure is actually immutable.

# Pattern Matching 

Going to keep this terse.
Pattern matching is assignment but it's also arithmetic equilibruim.

`x = 1` assigns 1 to x and returns the left hand side of the expression:

```
iex> x = 1
1
iex> x = 2
2
```

(Damn mutable references...)
The left side is always the assignable side. 
 
But lets say we make false claims about the expression on the other side.

```
iex> x = 1
1
iex> 2 = x
** (MatchError) no match of right hand side value: 2
```

Yep that fails.

So any variable will be re-assigned on the left. Hard assignments (constants) will fail so it should be fairly easy to see how constants (atoms) produce declarative code:

```
iex> {:ok, result} = dangerousOperation(input)
```

That will assign the result if it succeeds... You get the gist.
To drive that home, destructing in action with a Map:

```
iex> %{joe: a, jose: b, matz: c, rich: d} = %{joe: "Erlang", jose: "Elixir", matz: "Ruby", rich: "Clojure"}
%{joe: "Erlang", jose: "Elixir", matz: "Ruby", rich: "Clojure"}
iex> a "Erlang"
iex> b "Elixir"
iex> c "Ruby"
iex> d "Clojure"
```

as a real example, see `File.read` - pretty clear how atoms and pattern matching are used for structural declarative pattern matching.

```
iex(8)> h File.read

                                 def read(path)

Returns {:ok, binary}, where binary is a binary data object that contains the
contents of path, or {:error, reason} if an error occurs.

Typical error reasons:

  • :enoent  - the file does not exist
  • :eacces  - missing permission for reading the file, or for searching
    one of the parent directories
  • :eisdir  - the named file is a directory
  • :enotdir - a component of the file name is not a directory; on some
    platforms, :enoent is returned instead
  • :enomem  - there is not enough memory for the contents of the file

You can use :file.format_error/1 to get a descriptive string of the error.
```

_ is same as scala, haskell, etc.

## Lists

Yep like all functional languages... You guessed it - linked lists!

And you can desctructure them like any other functional langage with head (cell contents) and tail (remainder of the list). Each cell contains either nil ([]) or head and tail ([head | tail])

eg `[1, 2, 3] == [1 | [2 | [3 | []]]]`

Prepend is constant time:
```
iex(1)> list = [1, 2, 3] [1, 2, 3]
iex(2)> [0 | list ]
[0, 1, 2, 3]
You can use the ++ operator to concatenate lists: 
iex(3)> [0] ++ [1, 2, 3]
[0, 1, 2, 3]
```

As mentioned, [] is nil

```
iex(1)> [ head | tail ] = [:lonely] [:lonely]
iex(2)> head
:lonely
iex(3)> tail []
```

You can destructure lists as cons cells

```
iex> [head | tail] = [1, 2, 3] 
[1, 2, 3]
iex> head 
1
iex> tail 
[2, 3]
```

List module has all the usual monadic culprits

```
List.flatten [1, [:two], ["three", []]]
[1, :two, "three"]
```

Of course, we have the `h` command to help see the references:

```
iex(9)> h List

                                      List

Functions that work on (linked) lists.

Lists in Elixir are specified between square brackets:

    iex> [1, "two", 3, :four]
    [1, "two", 3, :four]

Two lists can be concatenated and subtracted using the Kernel.++/2 and
Kernel.--/2 operators:

    iex> [1, 2, 3] ++ [4, 5, 6]
    [1, 2, 3, 4, 5, 6]
    iex> [1, true, 2, false, 3, true] -- [true, false]
    [1, 2, 3, true]

Lists in Elixir are effectively linked lists, which means they are internally
represented in pairs containing the head and the tail of a list:

    iex> [head | tail] = [1, 2, 3]
    iex> head
    1
    iex> tail
    [2, 3]
```

So, use `h` work in the repl, use your usual functional idioms. 

# Pipe |>

Like unix pipe, throw a bunch of operations together, throwing the ouput into the next command input.

# Observer

One of the things Brent mentioned was the fact that you can check out the current running state of the VM. This is a big advantage over the JVM where you need to start the VM in a special way to plug in. Typically not a huge issue if you plan ahead, on prem installs have a different quality to them than your traditional platform builds as you own _nothing_.

From `iex` you can call `:observer.start` to get to the GUI. Like Akka, processes have supervision trees so have a gander and inspect other running processes.

# Processes

Processes in Erlang are actors - I know this coming into Elixir from some discussions I've had with people about actor model applications over the years: https://softwareengineering.stackexchange.com/questions/212754/when-is-it-not-good-to-use-actors-in-akka-erlang

Actor model implementation in Akka is based on erlang so it's very similar:

- supervision hierarchy, let it crash mentality
- location transparency
- isolation and message passing

And somewhat different:

- actors are processes (inversely, in erlang speak a process is an actor)
- processes have their own stack frame

# Project Management in Elixir

`mix` is the build tool you'll likely be using

Create a new project:

```
mix new bossman
```

Move into it

```
cd bossman
```

Test:

```
mix test
```

Dependencies are in mix.exs. Standard semver described dependencies.

```
 defmodule Bossman.Mixfile do
          use Mix.Project
          def project do
  [app: :bossman,
     version: "0.0.1",
     elixir: "~> 1.0",
     deps: deps]
end
  def application do
    [applications: [:logger, :httpoison]]
  end
  defp deps do
    [
    {:httpoison, "~> 0.9.0"},
    {:json,      "~> 0.3.0"}
    ]
end end
```

You can see the `defp deps do` section is where you put the dependencies.
Then the `def applications do` section has the used application dependencies described.

Not sure about test context yet... Todo

`mix` resolves the dependencies

