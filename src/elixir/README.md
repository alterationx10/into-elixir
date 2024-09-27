# The Language

`#` is used for comments. Block comments are not supported.

Variable assignment is called `binding`.

Rebinding doesn’t mutate the existing memory location. It reserves new memory
and reassigns the symbolic name to the new location.

A variable name always starts with a lowercase alphabetic character or an
underscore. After that, any combination of alphanumerics and underscores is
allowed. The prevalent convention is to use only lowercase ASCII letters,
digits, and underscores.

Variable names can also end with ? or !. Ending in ? is a convention to indicate
true/false return values. Ending in ! is a convention to indicate that the
function may raise an exception.

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
well, e.g. `func(a, b \\ 0, c \\ 0, d \\ 0)`.

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
