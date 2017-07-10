# [Credo](https://github.com/rrrene/credo)'s Elixir Style Guide [![Deps Status](https://beta.hexfaktor.org/badge/all/github/rrrene/elixir-style-guide.svg)](https://beta.hexfaktor.org/github/rrrene/elixir-style-guide)

## Prelude

There are two reasons for this document to exist:

- It is my personal style guide and consists both of the way I write Elixir today, but more importantly of things I've seen in the wild and adapted because they make Elixir more readable for open source Alchemists everywhere.
- Secondly, it is the [basis for Credo](https://github.com/rrrene/credo) and reflects the principles promoted by its code analysis.

Like all of my work, this style guide stands on the shoulders of giants: It is influenced by the Ruby style guides by [bbatsov](https://github.com/bbatsov/ruby-style-guide) and [GitHub](https://github.com/styleguide/ruby) as well as more [public](http://elixir.community/styleguide) [attempts](https://github.com/niftyn8/elixir_style_guide) at Elixir Style Guides.



## Philosophy

Contrary to other guides I've seen, this one is not very dogmatic. The overall principles are

* be consistent in your choices (i.e. apply the same rules everywhere),
* care about the readability of your code (e.g. when in doubt, spread text vertically rather than horizontally),
* and care about easier maintenance (avoid confusing names, etc.).

This is especially important because we are such a young community. All the **code we put out there is worth its weight in gold** if it is easy to comprehend and invites people to learn and contribute.



## Contribute

If you want to add to this document, please submit a pull request or open an issue to discuss specific points.


## The Actual Guide

### Code Readability

* <a name="spaces-indentation"></a>
  Use tabs consistently (2 spaces soft-tabs are **preferred**).
  <sup>[[link](#spaces-indentation)]</sup>

* <a name="line-endings"></a>
  Use line-endings consistently (Unix-style line endings are **preferred**, but we should not exclude our brothers and sisters riding the Redmond dragon).
  <sup>[[link](#line-endings)]</sup>

* <a name="no-trailing-whitespace"></a>
  Don't leave trailing white-space at the end of a line.
  <sup>[[link](#no-trailing-whitespace)]</sup>

* <a name="newline-eof"></a>
  End each file with a newline (some editors don't do this by default).
  <sup>[[link](#newline-eof)]</sup>

* <a name="spaces-operators"></a>
  Use spaces around operators and after commas.
  <sup>[[link](#spaces-operators)]</sup>

* <a name="spaces-braces"></a>
  Don't use spaces after `(`, `[`, and `{` or before `}`, `]`, and `)`. This is the **preferred** way, although other styles are possible, as long as they are applied consistently.
  <sup>[[link](#spaces-braces)]</sup>

  ```elixir
  # preferred way
  Helper.format({1, true, 2}, :my_atom)

  # also okay - carefully choose a style and use it consistently
  Helper.format( { 1, true, 2 }, :my_atom )
  ```

* <a name="character-per-line-limit"></a>
  Keep lines fewer than 80 characters whenever possible, although this is not a strict rule.
  <sup>[[link](#character-per-line-limit)]</sup>

* <a name="semicolon-between-statements"></a>
  Don't use `;` to separate statements and expressions.
  <sup>[[link](#semicolon-between-statements)]</sup>

  ```elixir
  # preferred way
  IO.puts "Waiting for:"
  IO.inspect object

  # NOT okay
  IO.puts "Waiting for:"; IO.inspect object
  ```

* <a name="no-space-bang"></a>
  Don't put a space after `!` to negate an expression.
  <sup>[[link](#no-space-bang)]</sup>

  ```elixir
  # preferred way
  denied = !allowed?

  # NOT okay
  denied = ! allowed?
  ```

* <a name="group-function-definitions"></a>
  Group function definitions. Keep the same function with different signatures together without separating blank lines. In all other cases, use blank lines to separate different functions/parts of your module (to maximize readability through "vertical white-space").
  <sup>[[link](#group-function-definitions)]</sup>

  ```elixir
  defp find_properties(source_file, config) do
    {property_for(source_file, config), source_file}
  end

  defp property_for(source_file, _config) do
    Enum.map(lines, &tabs_or_spaces/1)
  end

  defp tabs_or_spaces({_, "\t" <> line}), do: :tabs
  defp tabs_or_spaces({_, "  " <> line}), do: :spaces
  defp tabs_or_spaces({_, line}), do: nil
  ```

* <a name="vertical-space"></a>
  Generally use vertical-space to improve readability of sections of your code.
  <sup>[[link](#vertical-space)]</sup>

  ```elixir
  # it is preferred to employ a mixture of parentheses, descriptive variable
  # names and vertical white space to improve readability

  def run(%SourceFile{} = source_file, params \\ []) do
    source_file
    |> Helper.find_unused_calls(params, [:String], nil)
    |> Enum.reduce([], &add_to_issues/2)
  end

  defp add_to_issues(invalid_call, issues) do
    {trigger, meta, _} = invalid_call

    issues ++ [issue(meta[:line], trigger, source_file)]
  end

  # this function does the same as above, but is less comprehensible

  def run(%SourceFile{} = source_file, params \\ []) do
    Helper.find_unused_calls(source_file, params, [:String], nil)
    |> Enum.reduce [], fn {_, meta, _} = invalid_call, issues ->
      trigger = invalid_call |> Macro.to_string |> String.split("(") |> List.first
      issues ++ [issue(meta[:line], trigger, source_file)]
    end
  end

  ```

* <a name="pipe-chains"></a>
  It is **preferred** to start pipe chains with a "pure" value rather than a function call.
  <sup>[[link](#pipe-chains)]</sup>

  ```elixir
  # preferred way - this is very readable due to the clear flow of data
  username
  |> String.strip
  |> String.downcase

  # also okay - but often slightly less readable
  String.strip(username)
  |> String.downcase
  ```

* <a name="multi-line-call"></a>
  When assigning to a multi-line call, begin a new line after the `=`. Indent the assigned value's calculation by one level. This is the **preferred** way.
  <sup>[[link](#multi-line-call)]</sup>

  ```elixir
  # preferred way
  result =
    lines
    |> Enum.map(&tabs_or_spaces/1)
    |> Enum.uniq

  # also okay - align the first assignment and subsequent lines
  result = lines
           |> Enum.map(&tabs_or_spaces/1)
           |> Enum.uniq
  ```

* <a name="underscores-in-numerics"></a>
  Add underscores to large numbers for better readability.
  <sup>[[link](#underscores-in-numerics)]</sup>

  ```elixir
  # preferred way - very easy to read
  num = 10_000_000

  # NOT okay - how many zeros are there?
  num = 10000000
  ```

* <a name="function-parens"></a>
  Use `def`, `defp`, and `defmacro` with parentheses when the function takes parameters. Omit the parentheses when the function doesn't accept any parameters. This is the **preferred** way.
  <sup>[[link](#function-parens)]</sup>

  ```elixir
  # preferred way - omit parentheses for functions without parameters
  def time do
    # ...
  end

  # use parentheses if parameters are present
  def convert(x, y) do
    # ...
  end
  ```

* <a name="function-calling-parens"></a>
  Most of the time when calling functions that take parameters, it is **preferred** to use parentheses.
  <sup>[[link](#function-calling-parens)]</sup>

  ```elixir
  # preferred way - the more boring forms are preferred since it's easier to see what goes where
  Enum.reduce(1..100, 0, &(&1 + &2))

  Enum.reduce(1..100, 0, fn(x, acc) ->
    x + acc
  end)

  # also okay - carefully choose a style and use it consistently
  Enum.reduce 1..100, 0, & &1 + &2

  Enum.reduce 1..100, 0, fn x, acc ->
    x + acc
  end

  ```

* <a name="macro-parens"></a>
  For macros we see the contrary behaviour. The **preferred** way is to not use parentheses.
  <sup>[[link](#macro-parens)]</sup>

  ```elixir
  # preferred way
  defmodule MyApp.Service.TwitterAPI do
    use MyApp.Service, social: true

    alias MyApp.Service.Helper, as: H
  end
  ```

* <a name="conditional-parens"></a>
  Conclusively, never use parentheses around the condition of `if` or `unless` (since they are macros as well).
  <sup>[[link](#conditional-parens)]</sup>

  ```elixir
  # preferred way
  if valid?(username) do
    # ...
  end

  # NOT okay
  if( valid?(username) ) do
    # ...
  end
  ```

#### Naming

* <a name="camelcase-modules"></a>
  Use CamelCase for module names. It is **preferred** to keep acronyms like HTTP, XML uppercase.
  <sup>[[link](#camelcase-modules)]</sup>

  ```elixir
  # preferred way
  defmodule MyApp.HTTPService do
  end

  # also okay - carefully choose a style and use it consistently
  defmodule MyApp.HttpService do
  end
  ```

* <a name="snake-case-attributes-functions-macros-vars"></a>
  Use snake_case for module attribute, function, macro and variable names.
  <sup>[[link](#snake-case-attributes-functions-macros-vars)]</sup>

  ```elixir
  # preferred way
  defmodule MyApp.HTTPService do
    @some_setting :my_value

    def my_function(param_value) do
      variable_value1 = "test"
    end
  end

  # NOT okay
  defmodule MyApp.HTTPService do
    @someSetting :my_value

    def myFunction(paramValue) do
      variableValue1 = "test"
    end
  end
  ```

* <a name="exception-naming"></a>
  Exception names should have a common prefix or suffix. While this can be anything you like, esp. for small libraries, a common choice seems to have all of them end in `Error`.
  <sup>[[link](#exception-naming)]</sup>

  ```elixir
  # preferred way - common suffix Error
  defmodule BadHTTPHeaderError do
    defexception [:message]
  end

  defmodule HTTPRequestError do
    defexception [:message]
  end

  # also okay - consistent prefix Invalid
  defmodule InvalidHTTPHeader do
    defexception [:message]
  end

  defmodule InvalidUserRequest do
    defexception [:message]
  end

  # bad - there is no common naming scheme for exceptions
  defmodule InvalidHeader do
    defexception [:message]
  end

  defmodule RequestFailed do
    defexception [:message]
  end
  ```

* <a name="predicates"></a>
  Predicate functions/macros should return a boolean value.
  <sup>[[link](#predicates)]</sup>

  For functions, they should end in a question mark.

  ```elixir
  # preferred way
  def valid?(username) do
    # ...
  end

  # NOT okay
  def is_valid?(username) do
    # ...
  end
  ```

  For guard-safe macros they should have the prefix `is_` and not end in a question mark.

  ```elixir
  # preferred way
  defmacro is_valid(username) do
    # ...
  end

  # NOT okay
  defmacro valid?(username) do
    # ...
  end
  ```



#### Sigils

* <a name="sigils"></a>
  Use sigils where it makes sense, but don't use them in a dogmatic way.
  <sup>[[link](#sigils)]</sup>

  Example: Don't automatically use `~S` just because there is *one* `"` in your string, but start using it when you would have to escape a lot of double-quotes.

  ```elixir
  # preferred way - use normal quotes even if one has to be escaped
  legend = "single quote ('), double quote (\")"

  # use sigils when you would have to escape several quotes otherwise
  html = ~S(<a href="http://elixir-lang.org" target="_blank" rel="external">Homepage</a>)

  # also okay, but not preferred - important: choose a common sigil and stick with it
  # avoid using ~S{} in one place while using ~S(), ~S[] and ~S<> in others
  legend = ~S{single quote ('), double quote (")}
  html = "<a href=\"http://elixir-lang.org\" target=\"_blank\" rel=\"external\">Homepage</a>"
  ```

#### Regular Expressions

* <a name="regex-sigils"></a>
  Use `~r//` as your "go-to sigil" when it comes to Regexes as they are the easiest to read for people new to Elixir. That said, feel free to use other `~r` sigils when you have several slashes in your expression.
  <sup>[[link](#regex-sigils)]</sup>

  ```elixir
  # preferred way - use slashes because they are familiar regex delimiters
  regex = ~r/\d+/

  # use sigils when you would have to escape several quotes otherwise
  regex = ~r{http://elixir-lang.org/getting-started/mix-otp/(.+).html}
  ```

* <a name="caret-and-dollar-regex"></a>
  Be careful with `^` and `$` as they match start/end of line, not string endings. If you want to match the whole string use: `\A` and `\z`.
  <sup>[[link](#caret-and-dollar-regex)]</sup>




### Documentation

* <a name="documentation"></a>
  First, try to see documentation in a positive light. It is not a chore that you put off for as long as possible (forever). At some point in your project, starting to document parts of your project is an opportunity to communicate important things with future-maintainers, potential users of your API and even your future self.
  <sup>[[link](#documentation)]</sup>

* <a name="doc-false"></a>
  When that point in time comes, every module and function should either be documented or marked via `@moduledoc false`/`@doc false` to indicate that there is no intent to document the object in question.
  <sup>[[link](#doc-false)]</sup>

* <a name="exdoc"></a>
  Use ExDoc for this, it's great.
  <sup>[[link](#exdoc)]</sup>

* <a name="doc-style"></a>
  As for style, put an empty line after `@moduledoc`. Don't put an empty line between `@doc` and its function/macro definition.
  <sup>[[link](#doc-style)]</sup>

  ```elixir
  defmodule MyApp.HTTPService do
    @moduledoc false

    @doc "Sends a POST request to the given `url`."
    def post(url) do
      # ...
    end
  end
  ```

* <a name="doc-comments"></a>
  Although Elixir favors `@moduledoc` and `@doc` as first-class citizens, don't be afraid to communicate via normal code comments as well. But remember: don't use this to explain bad code!
  <sup>[[link](#doc-comments)]</sup>

  ```elixir
  # preferred way - provide useful additional information
  defmodule Credo.Issue do
    defstruct category:     nil,
              message:      nil,
              filename:     nil,
              line_no:      nil,
              column:       nil,
              trigger:      nil,  # optional: the call that triggered the issue
              metadata:     [],   # optional: filled in by the failing check
  end

  # NOT okay - "explaining" confusing code and ambiguous names
  defmodule AbstractCredoIssueInterfaceFactory do
    def build(input, params \\ []) do
      Helper.find_values(input, params) # input is either a username or pid
      |> Enum.reduce %{}, fn {_, meta, _} = value, list ->
        if valid?(value) do
          case Map.get(list, :action) do # remember: list is a map!!!!111
            nil -> nil # why does this break sometimes?
            val -> Map.put(list, val, true)
          end
        else
          list
        end
      end
    end
  end
  ```

* <a name="doc-comments-on-own-lines"></a>
  If necessary, put longer, more descriptive comments on their own line rather than at the end of a line of code.
  <sup>[[link](#doc-comments-on-own-lines)]</sup>



### Refactoring Opportunities


* <a name="no-nested-conditionals"></a>
  Never nest `if`, `unless`, and `case` more than 1 time. If your logic demands it, spread it over multiple functions.
  <sup>[[link](#no-nested-conditionals)]</sup>

  ```elixir
  # preferred way
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

  # NOT okay - rule of thumb: it starts to hurt at 3 levels of nesting
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

  ```

* <a name="no-unless-with-else"></a>
  Never use `unless` with else. Rewrite these with `if`, putting the positive case first.
  <sup>[[link](#no-unless-with-else)]</sup>

  ```elixir
  # without an else block:
  unless allowed? do
    raise "Not allowed!"
  end

  # preferred way to "add" an `else` block here: rewrite using `if`
  if allowed? do
    proceed_as_planned
  else
    raise "Not allowed!"
  end

  # NOT okay
  unless allowed? do
    raise "Not allowed!"
  else
    proceed_as_planned
  end
  ```

* <a name="avoid-double-negations"></a>
  Never use `unless` with a negated expression as condition. Rewrite with `if`.
  <sup>[[link](#avoid-double-negations)]</sup>

  ```elixir
  # preferred way
  if allowed? do
    proceed_as_planned
  end

  # NOT okay - rewrite using `if`
  unless !allowed? do
    proceed_as_planned
  end
  ```

* <a name="reference-current-module"></a>
  Always use `__MODULE__` when referencing the current module.
  <sup>[[link](#reference-current-module)]</sup>


### Software Design

* <a name="fixme"></a>
  Use `FIXME:` comments to mark issues/bugs inside your code.
  <sup>[[link](#fixme)]</sup>

  ```elixir
  defmodule MyApp do
    # FIXME: this breaks for x > 1000
    def calculate(x) do
      # ...
    end
  end
  ```

* <a name="todo"></a>
  Use `TODO:` comments to plan changes to your code.
  <sup>[[link](#todo)]</sup>

  ```elixir
  defmodule MyApp do
    # TODO: rename into something more clear
    def generic_function_name do
      # ...
    end
  end
  ```
  
  This way tools have a chance to find and report both `FIXME:` and `TODO:` comments.

* <a name="alias-modules"></a>
  When developing applications, try to alias all used modules. This improves readability and makes it easier to reason about the dependencies of a module inside your project. There are obvious exceptions for modules from Elixir's stdlib (e.g. `IO.ANSI`) or if your submodule has a name identical to an existing name (e.g. don't alias `YourProject.List` because that would override `List`). Like most other points in this guide, this is just a suggestion, not a strict rule.
  <sup>[[link](#alias-modules)]</sup>

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

  The thinking behind this is that you can see the dependencies of your module at a glance. So if you are attempting to build a medium to large project, **this can help you to get your boundaries/layers/contracts right**.



### Pitfalls

* <a name="iex-pry"></a>
  Never leave a call to `IEx.pry` in production code.
  <sup>[[link](#iex-pry)]</sup>

* <a name="io-inspect"></a>
  Be wary of calls to `IO.inspect` in production code. If you want to actually log useful information for later debugging, use a combination of `Logger` and `&inspect/1` instead.
  <sup>[[link](#io-inspect)]</sup>

* <a name="debugging-conditionals"></a>
  Conditionals should never contain an expression that always evaluates to the same value (such as `true`, `false`, `x == x`). They are most likely leftovers from a debugging session.
  <sup>[[link](#debugging-conditionals)]</sup>

* <a name="kernel-functions"></a>
  Be wary of naming variables and functions the same as functions defined in `Kernel`, especially in cases where the function has arity 0.
  <sup>[[link](#kernel-functions)]</sup>

* <a name="stdlib-modules"></a>
  Be wary of naming modules the same as modules in the stdlib. Sometimes `YourProject.DataTypeString` is a less error-prone choice as the seemingly cleaner `YourProject.DataType.String` because aliasing the later in a module makes the *normal* `String` module unavailable.
  <sup>[[link](#stdlib-modules)]</sup>



### Above all else

Follow your instincts. Write coherent code by applying a consistent style.



## License

This work is licensed under [the CC BY 4.0 license](https://creativecommons.org/licenses/by/4.0).
