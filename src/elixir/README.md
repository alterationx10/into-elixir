# The Language

`#` is used for comments. Block comments are not supported.

Variable assignment is called `binding`.

Rebinding doesn’t mutate the existing memory location. It reserves new memory
and reassigns the symbolic name to the new location.

A variable name always starts with a lowercase alphabetic character or an
underscore. After that, any combination of alphanumerics and underscores is
allowed. The prevalent convention is to use only lowercase ASCII letters,
digits, and underscores.

Variable names can also end with `?` or `!`. Ending in `?` is a convention to
indicate true/false return values. Ending in `!` is a convention to indicate
that the function may raise an exception.

A `module` is a collection of functions, somewhat like a namespace. Every Elixir
function must be defined inside a module. To define your own module, you use the
`defmodule` expression. Inside the module, you define functions using the `def`
expression (or `defp` to keep the function private).

To call a function of a module you use the syntax
`ModuleName.function_name(args)`. If a function being called resides in the same
module, the prefix is not needed. When calling functions, parentheses are
optional. `func(a, b)` is the same as `func a, b`.

A function is uniquely identified by its containing module, its name, and its
arity. Given `Module.fun(a,b)` and `Module.fun(a)`, they would be referred to as
`Module.fun/2` and `Module.fun/1`. This means it’s not possible to have a
function accept a variable number of arguments. Typically a lower-arity function
calls to a higher-arity function, providing some default arguments. This can be
automated by using `\\` in the function definition, e.g. `func(a, b \\ 0)`,
where `fun/2` and `fun/1` are generated. This works for multiple arguments as
well, e.g. `func(a, b \\ 0, c \\ 0, d \\ 0)`. Functions can also be bound to
variables, and be anonymous functions / lambdas. Lambdas are usually called with
a `.` for clarity. With `my_func.(arg)` you know it's an anonymous function, vs
`my_func(arg)` which could be a module function. When passing functions as
arguments it can be convenient to use the capture operator`&`. It takes the full
function qualifier — a module name, a function name, and an arity (e.g.
`&IO.puts/1`), and turns that function into a lambda that can be assigned to a
variable. It can also used when binding a lambda to a variable. When capturing a
variable as an argument to a lambda, the closure captures the memory address, so
rebinding the variable will not affect the lambda.

A module name must start with an uppercase letter and is usually written in
CamelCase style. A module name can consist of alphanumerics, underscores, and
the dot. The latter is often used to organize modules hierarchically (e.g.
`OuterModule.InnerModule.some_function(args)`). There is nothing special about
the dot character. The compiled version doesn’t record any hierarchical
relations between the modules. It’s typical to add some common prefix to all
module names in a project. Usually the application or the library name is used
for this purpose.

You can import a module to avoid having to prefix the module name when calling
its functions inside your module. E.g.

```elixir
defmodule MyModule do
  import AnotherModule
  def my_function do
    function_from_another_module()
  end
end
```

You can also `alias` a module to use a another name, e.g.

```elixir
defmodule MyModule do
  alias AnotherModule, as: AM
  def my_function do
    AM.function()
  end
end
```

or use the last portion of a longer module name without the `as` option, e.g.

```elixir
defmodule MyModule do
  alias AnotherModule.SomeModule
  def my_function do
    SomeModule.function()
  end
end
```

Module `attributes` are like compile-time constants, e.g. `@pi 3.14159`. An
attribute can be registered, which means it will be stored in the generated
binary and can be accessed at runtime. Elixir registers some module attributes
by default, like `@moduledoc` and `@doc` can be used to provide documentation
for modules and functions. `@spec` is another, which lets you to provide type
information for your functions, that can later be analyzed with a tool called
`dialyzer`

```elixir
defmodule MyModule do
  @moduledoc """
  This is a module documentation
  """
  @pi 3.14159

  @doc """
  This is a function documentation
  """
  @spec my_function(number) :: number
  def my_function(a) do
    2 * @pi * a * a
  end
end
```

`@moduledoc` and `@doc` are used by the `ex_doc` tool to generate documentation

Elixir has a built in pipe operator `|>` for chaining functions. It takes the
result of the expression on the left and passes it as the first argument to the
function on the right, e.g. `a |> f (b)` is the same as `f(a, b)`.

Elixir is a dynamically typed language. The types are:

- numbers
  - integers
  - floats
- boolean
- string
- atoms

The `/` operator always returns a `float`, use `div(a,b)` for integer division.

An `_` can be used as a visual digit separator, e.g. `1_000_000`.

There’s no upper limit on an integer’s size, and you can use arbitrarily large
numbers.

Atoms are literal named constants (kind of like an enumeration). They are
defined with a `:` followed by a combination of alphanumerics and/or underscore
characters (`:"even with spaces"`). They are all stored in an atom table at
runtime, so are efficient, especially for named constants. Atoms can be aliased,
by not using a `:` and starting with an uppercase letter. `MyAtom` will be set
to `:"Elixir.MyAtom"`.

Boolean surprise! The atoms `:true` and `:false` are the designated values for
booleans, and there is no dedicated type.

The atom `:nil` is used for null, and also factors into "truthiness" checks
(it's falsey). Elixir’s short-circuit logic operators are `||`, `&&` (with `!`
to negate). `||` returns the first expression that isn’t falsy. `&&` returns the
second expression, but only if the first expression is truthy. Otherwise, it
returns the first expression without evaluating the second one. These let you
intelligently chain functions/operations together, e.g. in

```elixir
database_value = connection && read_data(connection)
```

`read_data/1` will never be evaluated if `connection` is `nil`.

`Tuples` are untyped structures, and are surround with `{}`. E.g.

```elixir
{:ok, "Success"}
{:error, "Failure Msg"}
{"Alterationx10", 2, 3.14, :false}
```

Tuple elements can be accessed/put by index (indexing starts at 0).

Lists are surrounded by `[]` and are singly linked lists. To do something with
the list, you have to traverse it, so most of the operations on lists have an
O(n) complexity. Lists are never a good fit when direct access is called for. To
append a new element at the tail, you have to iterate and (shallow) copy the
entire list. Pushing an element to the top of a list doesn’t copy anything, so
is more efficient.

Maps (`%{}`)a key/value store, where keys and values can be any term. They have
dual usage in Elixir: to power dynamically sized key/value structures, and
manage simple records. Atom keys get special treatment, and can be used to
access in dot notation.

E.g. for simple records:

```elixir
user = %{:name: "Mark", :studying: "Elixir"}

user[:name]
# => "Mark"

user.name
# => "Mark"

```

Maps provide no guarantees about missing fields (`structs` can be used for
this).

```elixir
map = %{:a => 1, :b => 2}

map[:c]
# => nil

map.c
# => ** (KeyError) key :c not found
```

A `binary` (`<<>>`) is a chunk of bytes. `<<1, 2, 3>>` is a 3 byte binary
(default 8 bits). Values over 255 are truncated `<<256>> == <<0>>`. You can
specify the bits `<<256::16>> == <<1, 0>>`. This bit size does not need to be a
multiple of 8. If the total size of all the values isn’t a multiple of 8, the
binary is a sequence of bits called a `bitstring`.

There is no dedicated `string` type - they are either binaries or a list.

```elixir
"hello" == <<104, 101, 108, 108, 111>>
```

Multiline strings are allowed. You can use triple quotes (`heredocs`) for
"better" multiline strings. the last triple quote needs to go on it's own line.

```elixir
"
this
is multiline
"

"""
this is heredoc
I can put a " here, or """, and not worry about escaping
"""
```

Expressions in strings can be embedded/evaluated/interpolated with `#{}` e.g.
`"It's me! #{user.name}"`

Elixir has the concept of a `sigil`. Sigils start with `~` and is followed by
either a single lower-case letter or one or more upper-case letters, and then a
delimiter. You can declare a string with the `~s` sigil, e.g. `~s{hello}`. This
is useful when you want to escape characters `~s("hello", said the world.)`.
`~S` doesn’t handle interpolation or escape characters. `~r` is used for regular
expressions.

Strings can also be character lists (a list on integers), when used with single
quotes, e.g. `'ABC123'`. These can have interpolated values, and use the similar
`~c` and `~C` sigils.

Other basic types are

- reference
- pid
- port identifier

Higher level types are

- range
- keyword lists
- mapset
- times/dates
- IO lists
