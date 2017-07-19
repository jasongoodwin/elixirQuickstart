# elixir-fasttrack
Best way to get up and running is to write shit. This is a tradition for me.
Built for the scala-minded.
Really for me alone. It's brutally terse. It's enough to remind myself how things work as I go.

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
