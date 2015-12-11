# Elixir Style Guide

## Prelude

There are two reasons for this document to exist:

- It is my personal style guide and consists both of the way I write Elixir today, but more importantly of things I've seen in the wild and adapted because they make Elixir more readable for open source Alchemists everywhere.
- Secondly, it is the [basis for Credo](https://github.com/rrrene/credo) and reflects the principles promoted by its code analysis.

Like all of my work, this style guide stands on the shoulders of giants: It is influenced by the Ruby style guides by [bbatsov](https://github.com/bbatsov/ruby-style-guide) and [GitHub](https://github.com/styleguide/ruby) as well as more [public](http://elixir.community/styleguide) [attempts](https://github.com/niftyn8/elixir_style_guide) at Elixir Style Guides.



## Philosophy

Contrary to other guides I've seen, this one is not very dogmatic. The overall principles are

* be consistent in your choices (i.e. apply the same rules everywhere),
* care about the readibility of your code (e.g. when in doubt, spread text vertically rather than horizontally),
* and care about easier maintenance (avoid confusing names, etc.).

This is especially important because we are such a young community. All the **code we put out there is worth its weight in gold** if it is easy to comprehend and invites people to learn and contribute.



## Contribute

If you want to add to this document, please submit a pull request or open an issue to discuss specific points.


## The Actual Guide

### Code Readability

* Use tabs consistently (2 spaces soft-tabs are **preferred**).

* Use line-endings consistently (Unix-style line endings are **preferred**, but we should not exclude our brothers and sisters riding the Redmond dragon).

* Use spaces around operators and after commas.

* Don't use spaces after `(`, `[`, and `{` or before `}`, `]`, and `)`. This is the **preferred** way, although other styles are possible, as long as they are applied consistently.

  ```elixir
  # preferred way
  Helper.format({1, true, 2}, :my_atom)

  # also okay - choose a style and use it consistently
  Helper.format( { 1, true, 2 }, :my_atom )
  ```

* Keep lines fewer than 80 characters whenever possible, although this is not a strict rule.

* Don't leave trailing white-space at the end of a line.

* End each file with a newline (some editors don't do this by default).

* Don't use `;` to separate statements and expressions.

  ```elixir
  # preferred way
  IO.puts "Waiting for:"
  IO.inspect object

  # NOT okay
  IO.puts "Waiting for:"; IO.inspect object
  ```

* Don't put a space after `!` to negate an expression.

  ```elixir
  # preferred way
  denied = !allowed?

  # NOT okay
  denied = ! allowed?
  ```

* Group function definitions. Keep the same function with different signatures together without separating blank lines. In all other cases, use blank lines to separate different functions/parts of your module (to maximze readability through "vertical white-space").

  ```elixir
  defp find_properties(source_file, config) do
    {property_for(source_file, config), source_file}
  end

  defp property_for(source_file, _config) do
    lines
    |> Enum.map(&tabs_or_spaces/1)
  end

  defp tabs_or_spaces({_, "\t" <> line}), do: :tabs
  defp tabs_or_spaces({_, "  " <> line}), do: :spaces
  defp tabs_or_spaces({_, line}), do: nil
  ```

* Generally use vertical-space to improve readability of sections of your code.

  ```elixir
  # who can guess what this function does?

  def run(%SourceFile{} = source_file, params \\ []) do
    UnusedFunctionReturnHelper.find_unused_calls(source_file, params, [:String], nil)
    |> Enum.reduce all_unused_calls, [], fn {_, meta, _} = invalid_call, issues ->
      trigger = invalid_call |> Macro.to_string |> String.split("(") |> List.first
      issues ++ [issue(meta[:line], trigger, source_file)]
    end
  end

  # employ a mixture of parentheses, variables and vertical white space to
  # improve readability (and subsequently discover refactoring opportunities)

  def run(%SourceFile{} = source_file, params \\ []) do
    all_unused_calls =
      source_file
      |> UnusedFunctionReturnHelper.find_unused_calls(params, [:String], nil)

    all_unused_calls
    |> Enum.reduce([], fn(invalid_call, issues) ->
        {_, meta, _} = invalid_call

        trigger =
          invalid_call
          |> Macro.to_string
          |> String.split("(")
          |> List.first

        issues ++ [issue(meta[:line], trigger, source_file)]
      end)
  end
  ```

* It is **preferred** to start pipe chains with a "pure" value rather than a function call.

  ```elixir
  # While this is technically fine ...

  String.strip(username)
  |> String.downcase

  # ... this seems to be more readable, due to the clear flow of data:

  username
  |> String.strip
  |> String.downcase
  ```

* When assigning to a multi-line call, begin a new line after the `=`. Indent the assigned value's calculation by one level. This is the **preferred** way, ...

  ```elixir
  result =
    lines
    |> Enum.map(&tabs_or_spaces/1)
    |> Enum.uniq

  # while another variant is to align the first assignment and subsequent lines.

  result = lines
           |> Enum.map(&tabs_or_spaces/1)
           |> Enum.uniq
  ```

* Add underscores to large numbers for better readability.

  ```elixir
  # bad - how many zeros are there?
  num = 10000000

  # good - much easier to read
  num = 10_000_000
  ```


* Use `def`, `defp`, and `defmacro` with parentheses when the function takes parameters. Omit the parentheses when the function doesn't accept any parameters.

  ```elixir
  # omit parentheses for functions without parameters

  def time do
    # ...
  end

  # use parentheses if parameters are present

  def convert(x, y) do
    # ...
  end
  ```

* Most of the time when calling functions that take parameters, it is **preferred** to use parentheses.

  ```elixir
  # While this is super cool when new to the `&` special form and `fn` ...
  Enum.reduce 1..100, 0, & &1 + &2

  Enum.reduce 1..100, 0, fn x, acc ->
    x + acc
  end

  # the more boring forms are preferred since it's easier to see what goes where
  Enum.reduce(1..100, 0, &(&1 + &2))

  Enum.reduce(1..100, 0, fn(x, acc) ->
    x + acc
  end)
  ```

* For macros we see the contrary behaviour. The **preferred** way is to not use parentheses.

  ```elixir
  defmodule MyApp.Service.TwitterAPI do
    use MyApp.Service, social: true

    alias MyApp.Service.Helper, as: H
  end
  ```

* Conclusively, never use parentheses around the condition of `if` or `unless` (since they are macros as well).

#### Naming

* Use CamelCase for module names. It is **preferred** to keep acronyms like HTTP, XML uppercase.

  ```elixir
  # preferred way
  defmodule MyApp.HTTPService do
  end

  # also okay - choose a style and use it consistently
  defmodule MyApp.HttpService do
  end
  ```

* Use snake_case for module attribute, function, macro and variable names.

* Exception names should have a common prefix or suffix. While this can be anything you like, esp. for small libraries, a common choice seems to have all of them end in `Error`.

  ```elixir
  # bad - there is no common naming scheme for exceptions

  defmodule InvalidHeader do
    defexception [:message]
  end

  defmodule RequestFailed do
    defexception [:message]
  end

  # good - common suffix Error

  defmodule BadHTTPHeaderError do
    defexception [:message]
  end

  defmodule HTTPRequestError do
    defexception [:message]
  end

  # still okay - consistent prefix Invalid

  defmodule InvalidHTTPHeader do
    defexception [:message]
  end

  defmodule InvalidUserRequest do
    defexception [:message]
  end
  ```

* Predicate functions/macros should return a boolean value.

  For functions, they should end in a question mark.

  For guard-safe macros they should have the prefix `is_` and not end in a question mark.



#### Sigils

* Use sigils where it makes sense, but don't use them in a dogmatic way.

  Example: Don't automatically use `~S` just because there is *one* `"` in your string, but start using it when you would have to escape a lot of double-quotes.

  ```elixir
  # bad

  legend = ~S{single quote ('), double quote (")}
  html = "<a href=\"http://elixir-lang.org\" rel=\"external\">Click here</a>"

  # good

  legend = "single quote ('), double quote (\")"
  html = ~S(<a href="http://elixir-lang.org" rel="external">Click here</a>)
  ```

#### Regular Expressions

* Use `~r//` as your "go-to sigil" when it comes to Regexes as they are the easiest to read for people new to Elixir. That said, feel free to use other `~r` sigils when you have several slashes in your expression.

* Be careful with ^ and $ as they match start/end of line, not string endings. If you want to match the whole string use: \A and \z.



### Documentation

* First, try to see documentation in a positive light. It is not a choir that you put off for as long as possible (forever). At some point in your project, starting to document parts of your project is an opportunity to communicate important things with future-maintainers, potential users of your API and even your future self.

* When that point in time comes, every module and function should either be documented or marked via `@moduledoc false`/`@doc false` to indicate that there is no intent to document the object in question.

* Use ExDoc for this, it's great.

* As for style, put an empty line after `@moduledoc`. Don't put an empty line between `@doc` and its function/macro definition.

* Although Elixir favors `@moduledoc` and `@doc` as first-class citizens, don't be afraid to communicate via normal code comments as well. But remember: don't use this to explain bad code!

* Put longer, more descriptive comments on their own line rather than at the end of a line of code.



### Refactoring Opportunities


* Never nest `if`, `unless`, and `case` more than 1 time. If your logic demands it, spread it over multiple functions.

  ```elixir
  # While this does compile ...

  defp perform_task(valid, hash, config) do
    if valid do
      case Map.get(hash, :action) do
        :create ->
          # ...
        :delete ->
          if sid do   # <-- we reach three levels of nesting here :(
            # ...
          else
            # ...
          end
        nil ->
          nil
      end
    end
  end

  # ... it will be easier for your future-self to understand this:

  defp perform_task(false, hash, config) do
    nil
  end
  defp perform_task(true, hash, config) do
    hash
    |> Map.get(:action)
    |> perform_action(config)
  end

  defp perform_action(nil, _config) do
    nil
  end
  defp perform_action(:create, _config) do
    # ...
  end
  defp perform_action(:delete, config) do
    if config[:id] do
      # ...
    else
      # ...
    end
  end
  ```

* Never use `unless` with else. Rewrite these with `if`, putting the positive case first.

  ```elixir
  # So while this is fine:

  unless allowed? do
    raise "Not allowed!"
  end

  # This should be refactored:

  unless allowed? do
    raise "Not allowed!"
  else
    proceed_as_planned
  end

  # to look like this:

  if allowed? do
    proceed_as_planned
  else
    raise "Not allowed!"
  end
  ```

* Never use `unless` with a negated expression as condition. Rewrite with `if`.

  ```elixir
  # The code in this example ...

  unless !allowed? do
    proceed_as_planned
  end

  # ... should be refactored to look like this:

  if allowed? do
    proceed_as_planned
  end
  ```

* Always use `__MODULE__` when referencing the current module.


### Software Design

* When developing applications, try to alias all used modules. This improves readability and makes it easier to reason about the dependencies of a module inside your project. There are obvious exceptions for modules from Elixir's stdlib (e.g. `IO.ANSI`) or if you're submodule has a name identical to an existing name (e.g. don't alias `YourProject.List` because that would override `List`). Like most other points in this guide, this is just a suggestion, not a strict rule.


  ```elixir
  # While this is completely fine:

  defmodule Test do
    def something do
      MyApp.External.TwitterAPI.search(...)
    end
  end

  # ... you might want to refactor it to look like this:

  defmodule Test do
    alias MyApp.External.TwitterAPI

    def something do
      TwitterAPI.search(...)
    end
  end
  ```

* The thinking behind this is that you can see the dependencies of your module at a glance. So if you are attempting to build a medium to large project, **this can help you to get your boundaries/layers/contracts right**.



### Pitfalls

* Never leave a call to `IEx.pry` in production code.

* Be vary of calls to `IO.inspect` in production code. If you want to actually log useful information for later debugging, use Logger instead.

* Conditionals should never contain an expression that always evaluates to the same value (such as `true`, `false`, `x == x`). They are most likely leftovers from a debugging session.

* Be vary to name variables and functions the same as functions defined in `Kernel`, especially in cases where the function has arity 0.

* Be vary to name modules the same as modules in the stdlib. Sometimes `YourProject.DataTypeString` is a less error-prone choice as the seemingly cleaner `YourProject.DataType.String` because aliasing the later in a module makes the *normal* `String` module unavailable.



### Above all else

Follow your instincts. Write coherent code by applying a consistent style.



## License

This work is licensed under [the CC BY 4.0 license](https://creativecommons.org/licenses/by/4.0).
