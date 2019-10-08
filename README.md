# Ruby Styleguide

This is the coding style guide we use at FreeAgent for our Ruby apps. We encourage you to set up one
that works for your own team.

Much of this was based on [the GitHub Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide).
Feel free to fork the guide but we won't accept pull requests from non-FreeAgent staff, unless they're
for typos etc.

## Coding Style

- Use two **spaces** per indentation level (aka soft tabs). No hard tabs.

```Ruby
# bad - four spaces
def some_method
    do_something
end

# good
def some_method
  do_something
end
```

- Keep lines equal to or fewer than 115 characters. *(Width of github's diff view without wrapping.)*

- Never leave trailing whitespace.

- End each file with a blank newline.

- Use spaces around operators, after commas, colons and semicolons. Use spaces around `{` and before `}` in blocks.

```ruby
  sum = 1 + 2
  a, b = 1, 2
  1 > 2 ? true : false; puts "Hi"
  [1, 2, 3].each { |e| puts e }
```
- No spaces after `(`, `[` or before `]`, `)`. No spaces after `{` and before `}` in hash declarations.


```ruby
  some(arg).other
  [1, 2, 3].length
```

- No spaces after `!`.

```ruby
  !array.include?(element)
```

- Indent `when` as deep as the corresponding `end`.

```ruby
  case
  when song.name == "Misty"
    puts "Not again!"
  when song.duration > 120
    puts "Too long!"
  when Time.now.hour > 21
    puts "It's too late"
  else
    song.play
  end


  kind = case year
  when 1850..1889 then "Blues"
  when 1890..1909 then "Ragtime"
  when 1910..1929 then "New Orleans Jazz"
  when 1930..1939 then "Swing"
  when 1940..1950 then "Bebop"
  else "Jazz"
  end
```

- Use empty lines between method (`def`) blocks, and within methods to break up method code into logical paragraphs.


```ruby
  def some_method
    data = initialize(options)

    data.manipulate!

    data.result
  end

  def some_method
    result
  end
```

- Last line of a multiline array or hash should end with a trailing comma. It keeps diffs much smaller when adding or deleting lines in future.

```ruby
  [
    "one",
    "two",
  ]
```

- Last element of an array or hash on a single line should omit the trailing comma however.

```ruby
  # Bad
  ["one", 2,]

  # Good
  ["one", 2]
```

## Documentation

Use [TomDoc](http://tomdoc.org) to the best of your ability. It's pretty sweet:

```ruby
  # Public: Duplicate some text an arbitrary number of times.
  #
  # text - The String to be duplicated.
  # count - The Integer number of times to duplicate the text.
  #
  # Examples
  #
  # multiplex("Tom", 4)
  # # => "TomTomTomTom"
  #
  # Returns the duplicated String.
  def multiplex(text, count)
    text * count
  end
```

To check and generate documentation install Yard with TomDoc Plugin

```bash
gem install yard yard-tomdoc
```

Run your isolated file through the documentation parser

```bash
yard doc --plugin tomdoc $FILENAME
open doc/index.html
```

_You do not need to commit the generated `./doc` or `.yardoc` files._

## Syntax

- Use `def` with parentheses when there are arguments. Omit the parentheses when the method doesn't accept any arguments.

```ruby
  def some_method
    # body omitted
  end

  def some_method_with_arguments(arg1, arg2)
    # body omitted
  end
```

- Never use `for`, unless you know exactly why. Most of the time iterators should be used instead. `for` is implemented in terms of `each` (so you're adding a level of indirection), but with a twist - `for` doesn't introduce a new scope (unlike `each`) and variables defined in its block will be visible outside it.

```ruby
  arr = [1, 2, 3]

  # bad
  for elem in arr do
    puts elem
  end

  # good
  arr.each { |elem| puts elem }
```

- Never use `then` for multi-line `if/unless`.

```ruby
  # bad
  if some_condition then
    # body omitted
  end

  # good
  if some_condition
    # body omitted
  end
```

- Avoid the ternary operator (`?:`) except in cases where all expressions are extremely trivial. However, do use the ternary operator(`?:`) over `if/then/else/end` constructs for single line conditionals.


```ruby
  # bad
  result = if some_condition then something else something_else end

  # good
  result = some_condition ? something : something_else
```

- Use one expression per branch in a ternary operator. This also means that ternary operators must not be nested. Prefer `if/else` constructs in these cases.

```ruby
  # bad
  some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

  # good
  if some_condition
    nested_condition ? nested_something : nested_something_else
  else
    something_else
  end
```

- The `and` and `or` keywords are banned. It's just not worth it. Always use `&&` and `||` instead.

- Avoid multi-line `?:` (the ternary operator), use `if/unless` instead.

- Favor modifier `if/unless` usage when you have a single-linebody.

```ruby
  # bad
  if some_condition
    do_something
  end

  # good
  do_something if some_condition
```

- Never use `unless` with `else`. Rewrite these with the positive case first.

```ruby
  # bad
  unless success?
    puts "failure"
  else
    puts "success"
  end

  # good
  if success?
    puts "success"
  else
    puts "failure"
  end
```

- Don't use parentheses around the condition of an `if/unless/while`.

```ruby
  # bad
  if (x > 10)
    # body omitted
  end

  # good
  if x > 10
    # body omitted
  end
```

- Prefer `{...}` over `do...end` for single-line blocks. Avoid using `{...}` for multi-line blocks (multiline chaining is always ugly). Always use `do...end` for "control flow" and "method definitions" (e.g. in Rakefiles and certain DSLs). Avoid `do...end` when chaining.

```ruby
  names = ["Bozhidar", "Steve", "Sarah"]

  # good
  names.each { |name| puts name }

  # bad
  names.each do |name|
    puts name
  end

  # good
  names.select { |name| name.start_with?("S") }.map { |name| name.upcase }

  # bad
  names.select do |name|
    name.start_with?("S")
  end.map { |name| name.upcase }
```

Some will argue that multiline chaining would look OK with the use of {...}, but they should ask themselves - is this code really readable and can't the block's contents be extracted into nifty methods?

- Avoid `return` where not required.

```ruby
  # bad
  def some_method(some_arr)
    return some_arr.size
  end

  # good
  def some_method(some_arr)
    some_arr.size
  end
```

- Use spaces around the `=` operator when assigning default values to method parameters:

```ruby
  # bad
  def some_method(arg1=:default, arg2=nil, arg3=[])
    # do something...
  end

  # good
  def some_method(arg1 = :default, arg2 = nil, arg3 = [])
    # do something...
  end
```

While several Ruby books suggest the first style, the second is much more prominent in practice (and arguably a bit more readable).

- Using the return value of `=` (an assignment) is ok.

```ruby
  # bad
  if (v = array.grep(/foo/)) ...

  # good
  if v = array.grep(/foo/) ...

  # also good - has correct precedence.
  if (v = next_value) == "hello" ...
```

- Use `||=` freely to initialize variables.

```ruby
  # set name to Bozhidar, only if it's nil or false
  name ||= "Bozhidar"
```

- Don't use `||=` to initialize boolean variables. (Consider what would happen if the current value happened to be `false`.)

```ruby
  # bad - would set enabled to true even if it was false
  enabled ||= true


  # good
  enabled = true if enabled.nil?
```

- Avoid using Perl-style special variables (like `$0-9`, `$`,etc. ). They are quite cryptic and their use in anything but one-liner scripts is discouraged. Prefer long form versions such as`$PROGRAM_NAME`.

- Never put a space between a method name and the opening parenthesis.

```ruby
  # bad
  f (3 + 2) + 1

  # good
  f(3 + 2) + 1
```

- If the first argument to a method begins with an open parenthesis, always use parentheses in the method invocation. For example, write`f((3 + 2) + 1)`.

- Prefix with `_` unused block parameters and local variables. It's
  also acceptable to use just `_` (although it's a bit less
  descriptive). This convention is recognized by the Ruby interpreter
  and tools like RuboCop and will suppress their unused variable warnings.

    ```ruby
    # bad
    result = hash.map { |k, v| v + 1 }

    def something(x)
      unused_var, used_var = something_else(x)
      # ...
    end

    # good
    result = hash.map { |_k, v| v + 1 }

    def something(x)
      _unused_var, used_var = something_else(x)
      # ...
    end

    # good
    result = hash.map { |_, v| v + 1 }

    def something(x)
      _, used_var = something_else(x)
      # ...
    end
    ```
- Don't use the `===` (threequals) operator to check types. `===` is mostly an implementation detail to support Ruby features like `case`, and it's not commutative. For example, `String === "hi"` is true and `"hi" === String` is false. Instead, use `is_a?` or `kind_of?` if you must.

Refactoring is even better. It's worth looking hard at any code that explicitly checks types.

- Avoid `::` when nesting modules (at least in the application)

```ruby
  # not good
  module Foo; end
  module Foo::Bar; end
  module Foo::Bar::Baz
    def self.n
      puts Module.nesting.inspect # => [Foo::Bar::Baz]
    end
  end

  # good

  module Foo; end

  module Foo
    module Bar; end
  end

  module Foo
    module Bar
      module Baz
        def self.n
          puts Module.nesting.inspect # => [Foo::Bar::Baz, Foo::Bar, Foo]
        end
      end
    end
  end
```

This can prevent complications when it comes to constant lookup.

## Naming

- Use `snake_case` for methods and variables.

- Use `CamelCase` for classes and modules. (Keep acronyms like HTTP, RFC, XML uppercase.)

- Use `SCREAMING_SNAKE_CASE` for other constants.

- The names of predicate methods (methods that return a boolean value) should end in a question mark. (i.e. `Array#empty?`).

- The names of potentially "dangerous" methods (i.e. methods that modify `self` or the arguments, `exit!`, etc.) should end with an exclamation mark. Bang methods should only exist if a non-bang method exists. ([More on this](http://dablog.rubypal.com/2007/8/15/bang-methods-or-danger-will-rubyist)).



## Classes

- Avoid the usage of class (`@@`) variables due to their unusual behavior in inheritance.

```ruby
  class Parent
    @@class_var = "parent"

    def self.print_class_var
      puts @@class_var
    end
  end

  class Child < Parent
    @@class_var = "child"
  end

  Parent.print_class_var # => will print "child"
```

As you can see all the classes in a class hierarchy actually share oneclass variable. Class instance variables should usually be preferred over class variables.

- Use `def self.method` to define singleton methods. This makes the methods more resistant to refactoring changes.

```ruby
  class TestClass
    # bad
    def TestClass.some_method
      # body omitted
    end

    # good
    def self.some_other_method
      # body omitted
    end
  end
```

- Avoid `class << self` except when necessary, e.g. single accessors and aliased attributes.

```ruby
  class TestClass
    # bad
    class << self
      def first_method
        # body omitted
      end

      def second_method_etc
        # body omitted
      end
    end

    # good
    class << self
      attr_accessor :per_page
      alias_method :nwo, :find_by_name_with_owner
    end

    def self.first_method
      # body omitted
    end

    def self.second_method_etc
      # body omitted
    end
  end
```

- Indent the `public`, `protected`, and `private` methods as much the method definitions they apply to. Leave one blank line above and below them.

```ruby
  class SomeClass
    def public_method
      # ...
    end

    private

    def private_method
      # ...
    end
  end
```

- Avoid explicit use of `self` as the recipient of internal class or instance messages unless to specify a method shadowed by a variable.

```ruby
  class SomeClass
    attr_accessor :message

    def greeting(name)
      message = "Hi #{name}" # local variable in Ruby, not attribute writer
      self.message = message
    end
  end
```

- Avoid explicit use of instance variables

```ruby
  # bad
  class SomeClass
    def initialize(foo)
      @foo = foo
    end
  end

  def foo?
    @foo == "Foo"
  end

  # good
  class SomeClass
    attr_reader :foo

    def initialize(foo)
      @foo = foo
    end

    def foo?
      foo == "Foo"
    end
  end

  # better (if `foo` doesn't need to be public)
  class SomeClass
    def initialize(foo)
      @foo = foo
    end

    def foo?
      foo == "Foo"
    end

    private

    attr_reader :foo

  end
```

- Avoid complex logic in the initialiser

```ruby
  # bad
  class SomeClass
    def initialize(foo)
      @foo = foo
      @bar = some_method(foo)
    end
  end

  # good
  class SomeClass
    def initialize(foo)
      @foo = foo
    end

    def bar
      @bar ||= some_method(foo)
    end
  end

  # good (using a class method)
  class SomeClass
    attr_reader :foo

    def self.from_id(id)
      new(Foo.find(id))
    end

    def initialize(foo)
      @foo = foo
    end
  end

  # good (using an instance method)
  class SomeClass
    def initialize(foo_id)
      @foo_id = foo_id
    end

    def foo
      @foo ||= Foo.find(foo_id)
    end

    private

    attr_reader :foo_id
  end
```

## Exceptions

- Don't use exceptions for flow of control.

```ruby
  # bad
  begin
    n / d
  rescue ZeroDivisionError
    puts "Cannot divide by 0!"
  end


  # good
  if d.zero?
    puts "Cannot divide by 0!"
  else
    n / d
  end
```

- Don't use bare rescues or rescue the `Exception` class.

```ruby
  # bad
  begin
    # an exception occurs here
  rescue Exception => e
    # exception handling
  end

  # bad
  begin
    # an exception occurs here
  rescue => e
    # exception handling
  end

  # good
  begin
    # an exception occurs here
  rescue StandardError => e
    # error handling here
  end
```

- Use the letter `e` for your short rescue variable.

```ruby
  # bad
  begin
    # an exception occurs here
  rescue StandardError => ex
    # exception handling
  end

  # good
  begin
    # an exception occurs here
  rescue StandardError => e
    # error handling here
  end
```

- Skip the rescue variable if you aren't going to use it.

```ruby
  # bad
  begin
    # an exception occurs here
  rescue StandardError => e
    Rails.logger.error("A problem happened!")
  end

  # good
  begin
    # an exception occurs here
  rescue StandardError
    Rails.logger.error("A problem happened!")
  end
```

## Collections

- Prefer `%w` to the literal array syntax when you need an array of strings.

```ruby
  # bad
  STATES = ["draft", "open", "closed"]

  # good
  STATES = %w(draft open closed)
```

- Use `Set` instead of `Array` when dealing with unique elements. `Set` implements a collection of unordered values with no duplicates. This is a hybrid of `Array`'s intuitive inter-operation facilities and `Hash`'s fast lookup.

- Use symbols instead of strings as hash keys where possible.

```ruby
  # bad
  hash = { "one" => 1, "two" => 2, "three" => 3 }

  # good
  hash = { :one => 1, :two => 2, :three => 3 }
```

- When splitting a hash over multiple lines, place one key/value pair per line
  with the closing brace on the line after the last key/value pair.

- Indent the contents of multiline hashes one level deeper than the preceeding
  code, don't line the hash up with the braces.

```ruby
  # bad
  hash = Contact.create(first_name: "Robert",
                         last_name: "Burns",
                         email: "haggis@burns.net")

  # good
  hash = Contact.create(
    first_name: "Robert",
    last_name:  "Burns",
    email:      "haggis@burns.net",
  )
```

- Drop `{}` around arguments when the there is only one hash as the argument, whether parens are included or not

```ruby
  # bad
  hash = Contact.create({first_name: "Robert", last_name: "Burns"})

  # good
  hash = Contact.create(first_name: "Robert", last_name: "Burns")

  # also good
  hash = Contact.create first_name: "Robert", last_name: "Burns"
```

- Add spacing to line up the hash rockets and/or values in columns if it helps
  readability.

- Don't use symbols where you have dynamic key names.

```ruby
  # bad
  hash = {:"user_#{id}" => "fred"}
```

```ruby
  hash = {one: 1, two: 2, three: 3}
```

## Strings

- Prefer string interpolation instead of string concatenation:

```ruby
  # bad
  email_with_name = user.name + " <" + user.email + ">"

  # good
  email_with_name = "#{user.name} <#{user.email}>"
```

- Prefer double-quoted strings. Interpolation and escaped characters will always work without a delimiter change, and `'` is a lot more common than `"` in string literals.

```ruby
  # bad
  name = 'Bozhidar'

  # good
  name = "Bozhidar"
```

- Avoid using `String#+` when you need to construct large data chunks. Instead, use `String#<<`. Concatenation mutates the string instance in-place and is always faster than `String#+`, which creates a bunch of new string objects.

```ruby
  # good and also fast
  html = ""
  html << "<h1>Page title</h1>"

  paragraphs.each do |paragraph|
    html << "<p>#{paragraph}</p>"
  end
```

- Add the `# frozen_string_literal: true` to the top of all files. This implicitly freezes all the string literals created in that file, which puts less pressure on garbage collection.

```ruby
# frozen_string_literal: true
class Foo
  def initialize
    string = "I'm frozen!"
  end
end
```

## Regular Expressions

- Avoid using `$1-9` as it can be hard to track what they contain. Named groups can be used instead.

```ruby
  # bad
  /(regexp)/ =~ string
  ...
  process $1


  # good
  /(?<meaningful_var>regexp)/ =~ string
  ...
  process meaningful_var
```

- Be careful with `^` and `$` as they match start/end of line, not string endings. If you want to match the whole string use: `\A` and `\z`.

```ruby
  string = "some injection\nusername"
  string[/^username$/]   # matches
  string[/\Ausername\z/] # don't match
```

- Use `x` modifier for complex regexps. This makes them more readable and you can add some useful comments. Just be careful as spaces are ignored.

```ruby
  regexp = %r{
    start         # some text
    \s            # white space char
    (group)       # first group
    (?:alt1|alt2) # some alternation
    end
  }x
```

## Percent Literals

- Use `%w` freely.

```ruby
  STATES = %w(draft open closed)
```

- Use `%()` for single-line strings which require both interpolation and embedded double-quotes. For multi-line strings, prefer heredocs.

```ruby
  # bad (no interpolation needed)
  %(<div class="text">Some text</div>)
  # should be "<div class=\"text\">Some text</div>"


  # bad (no double-quotes)
  %(This is #{quality} style)
  # should be "This is #{quality} style"


  # bad (multiple lines)
  %(<div>\n<span class="big">#{exclamation}</span>\n</div>)
  # should be a heredoc.


  # good (requires interpolation, has quotes, single line)
  %(<tr><td class="name">#{name}</td>)
```

- Use `%r` only for regular expressions matching _more than_ one '/' character.

```ruby
  # bad
  %r(\s+)

  # still bad
  %r(^/(.*)$)
  # should be /^\/(.*)$/

  # good
  %r(^/blog/2011/(.*)$)
```

## Above all else

Follow your ![:heart:](https://github.global.ssl.fastly.net/images/icons/emoji/heart.png ":heart:")
