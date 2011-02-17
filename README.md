# Elixir

Elixir is a programming language built on top of Erlang. As Erlang, it is a functional language with strict evaluation, single assignment and dynamic typing built to support distributed, fault-tolerant, non-stop applications with hot swapping. Elixir allows you to invoke Erlang modules without a need to convert data types, therefore there is no hit in performance when invoking existing Erlang code.

The main difference between Elixir and Erlang is syntax and object orientation. Elixir provides a very simple Object Model based on prototype languages with most of its syntax based on Ruby.

# Usage

Elixir is not ready for use yet, though this milestone will be achieved soon. If you want to contribute to Elixir, you can do that by cloning the repository and running ``make test``.

The tests are organized in two directories: `test/erlang` and `test/elixir`. The first are written in Erlang and the second in Elixir. Much of Elixir's standard library is written in Elixir and tested in Elixir. This makes fairly easy to improve the language.

# Roadmap

* Add case/match expressions
* Add exceptions
* Add interactive elixir (iex)
* Implement missing types and improve STDLIB
* Add generators/list comprehensions
* Add metaprogramming
* Add partial application, pipeline f1 + f2, and 1#add and Integer##add
* Add _.foo
* Add load paths
* Support guards
* Add JIT on module compilation
* Extending builtin types (like inheriting from Integer)
* Add method cache table
* Allow object definitions to be reopened or to copy from another object
* Improve constant lookup

# Learning Elixir

This is a basic introduction into Elixir basic objects and object model. Some sections have a paragraph called "To be implemented", they represent parts of Elixir that was not implemented yet and that are under discussion.

This introduction borrowed its guidelines from [Learn You Some Erlang](http://learnyousomeerlang.com/), a great resource to learn Erlang which will be referenced several times during this introduction.

## Some notation

Comments in Elixir are, as in Erlang, done with %.

    % This is a commented line

Throughout this introduction, `% =>` represents the result of an expression:

    1 + 1 % => 2

## Basic Types

### Numbers

Elixir supports both Integer and Floats:

    2 + 15       % => 17
    - 13 * 10    % => 130
    1986 - 1985  % => 1
    5 / 2        % => 2.5
    4 / 2        % => 2.0

Notice that, as Erlang, "/" always returns a Float. If you want to have integer-to-integer division and the modulo operator, you should use `div` and `rem`:

    5 div 2  % => 2
    5 rem 2  % => 1

Several operations can also be done in a single expression, obeying the normal precedence rules:

    50 * 100 - 490     % => 10
    (50 * 100) - 490   % => 10
    -(50 * 100) - 490  % => -990

As in Ruby, everything is an object, so we can call methods on numbers:

    -1.abs    % => 1
    5.div(2)  % => 2

It comes as no surprise that + is also a method:

    1.+(2)  % => 3

#### Documentation:

* <https://github.com/josevalim/elixir/tree/master/lib/integer.ex>
* <https://github.com/josevalim/elixir/tree/master/lib/float.ex>
* <https://github.com/josevalim/elixir/tree/master/lib/numeric.ex>

> #### To be implemented
>
> Currently, there is no support to enter numbers in bases other than base 10. This is the current API in Erlang:
>
>     2#101010.  % => 42
>     8#0677.    % => 447
>     16#AE.     % => 174
>
> Another feature that will likely be included is the ability to include "_" in numbers as in Ruby. This improves the readability when working with large numbers:
>
>     1_052_672

### Atoms

Elixir also has Atoms, called Symbols in other languages like Ruby. Although its syntax was borrowed from Lisp:

    'atom
    'Atom
    'atom_without_spaces

Atoms are literals, with their own value as name. An atom 'symbol is an atom 'symbol everywhere, with exactly the same value. Atoms start with a single quote and should not have spaces (spaces delimit the atom end). Atoms with spaces are represented by wrapping them in quotes:

    '"Atom with Spaces"

As in Erlang and Ruby, Atoms are not garbage collected, so remember to not generate atoms dynamically, otherwise you will run out of memory sooner rather than later.

#### Documentation:

* <https://github.com/josevalim/elixir/tree/master/lib/atom.ex>

### Booleans

As in Erlang, the boolean values are simply atoms named true and false. However, to avoid writing 'true and 'false, Elixir also allows you to simply write true or false. The following are all equivalent and will yield `1` as result:

    if 'true
      1
    else
      2
    end

    if true
      1
    else
      2
    end

    if 'false
      2
    else
      1
    end

    if false
      2
    else
      1
    end

> #### To be implemented
>
> Elixir currently lacks much of boolean algebra. None of the boolean operators were implemented yet:  `and`, `or`, `xor` nor `not`.
> Equality and match operators were not implemented as well: `==`, `/=`, `=:=`, `>`, `<`, etc.

### Tuples

Tuples are used to organize many terms together when you know how many terms there are. As in Erlang, a tuple is written in the following form:

    % A tuple containing all boolean values
    { true, false }

    % A tuple that may represent a point with coordinates X and Y
    { 10, 20 }

    % An empty tuple
    { }

#### Documentation:

* <https://github.com/josevalim/elixir/tree/master/lib/tuple.ex>

> #### To be implemented
>
> Currently there is no way to interact with tuples elements. The following API is proposed:
>
>   { 'a, 'b, 'c }[0]  % => 'a
>   { 'a, 'b, 'c }[1]  % => 'b
>   { 'a, 'b, 'c }[2]  % => 'c
>
> Setting an element will be done through the set method:
>
>   { 'a, 'b, 'c }.set(0, 'd)  % => { 'd, 'b, 'c }
>
> This same note is also valid for lists and dicts (which we are going to see next).

### Lists

Lists are the main object in Elixir (as in any other functional language) and can contain anything:

    % Some list with elements
    ['atom, 1, 2, 3, { 'some, 'tuple }]

    % An empty list
    []

Elixir Standard Library has a bunch of methods to interact with lists:

    [1, 2, 3].head         % => 1
    [1, 2, 3].tail         % => [2,3]
    [1, 2, 3].length       % => 3

As in Elixir `+` is simply a method like any other (and not an arithmetic operator as in Erlang), it can also be used to add arrays:

    [1, 2, 3] + [4, 5, 6]  % => [1,2,3,4,5,6]

However, lists in Erlang and Elixir are implemented as linked lists. This means prepending an item to the list is quite fast, but appending is much slower. Therefore we have a special syntax to prepend one or more items to a list:

    list = [2,3,4]

    % Don't do this:
    [1]   + [2,3,4]  % => [1,2,3,4]
    [0,1] + [2,3,4]  % => [0,1,2,3,4]

    % Do this instead:
    [1|list]    % => [1,2,3,4]
    [0,1|list]  % => [0,1,2,3,4]

Most of the power in lists comes when used together with functions:

    [1, 2, 3].map do (x)
      x * 2
    end  % => [2, 4, 6]

    [1, 2, 3].foldl 0, do (x, acc)
      acc + x
    end  % => 6

The examples above uses functions using the `do/end` syntax. Don't worry about them now, we are going to take a better look at them later.

#### Nil value

A nil value in Elixir is represented by an empty list. In if expressions (that we briefly saw above), everything evaluates to `true`, except empty lists `[]` and `false`. Both examples below will return 1 as result:

    if 'any_symbol
      1
    else
      2
    end

    if []
      2
    else
      1
    end

#### Documentation:

* <https://github.com/josevalim/elixir/tree/master/lib/list.ex>

### Ordered Dicts

Elixir provides a first-class syntax to deal with ordered dictionaries (similar to Hashes in Ruby).

    % A dict with 'a and 'b as keys and 1 and 2 as their respective values
    { 'a: 1, 'b: 2 }

    % An empty dict
    {:}

Elixir dictionary implementation is backed up by [the orddict module](http://www.erlang.org/doc/man/orddict.html) in OTP. Notice that Erlang ordered dicts are **not** ordered in the order items are added, but rather using Erlang ordering of terms. You can learn more about Erlang ordering by [reading this section from Learn You Some Erlang](http://learnyousomeerlang.com/starting-out-for-real#bool-and-compare).

Ordered Dicts are recommended to deal with small amount of data. Other data structures are recommended to deal with a huge amount and you can [read more about others key-value store](http://learnyousomeerlang.com/a-short-visit-to-common-data-structures#key-value-stores), but remember that most of them are not implemented in Elixir yet.

#### Documentation:

* <https://github.com/josevalim/elixir/tree/master/lib/ordered_dict.ex>

### Binaries

Elixir has a similar syntax to Erlang for handling binaries:

    % A binary with three elements
    <<1, 17, 42>>

    % Converting a binary to a list
    <<1, 17, 42>>.to_list  % => [1, 17, 42]

Elixir also allows to specify the size for binaries, using the same syntax as Erlang:

    % A binary with size 4, because we specify that 42 is a 16-bits segment
    <<1, 17, 42:16>>

By default, the binary type in both Elixir and Erlang is integer. That said, the following is invalid:

    <<3.14>>

Instead, you need explicitly specify it as a float:

    <<3.14|float>>

Notice the syntax above is a bit different from Erlang. Erlang uses `/` to specify the type, Elixir uses `|`. This allows Elixir, differently from Erlang, to have expressions inside binaries:

    <<1+2>>

In general, everything that applies to Erlang binaries applies to Elixir binaries. You can [read more about them on Erlang's documentation](http://www.erlang.org/doc/programming_examples/bit_syntax.html).

#### Documentation

* <https://github.com/josevalim/elixir/tree/master/lib/binary.ex>
* <http://www.erlang.org/doc/programming_examples/bit_syntax.html>

### Strings

In Erlang, strings are a list of chars:

    "hello" == [104, 101, 108, 108, 111]

This is expensive because each character uses 8 bytes of memory, not 8 bits! Erlang stores each character as a 32-bit integer, with a 32-bit pointer for the next item in the list.

Elixir takes a different approach to strings. Strings in Elixir are handled as UTF-8 binaries.

    % The famous "hello world" string
    "hello world"

    % A string converted to its underlying binary:
    "hello".to_bin  % => <<[104, 101, 108, 108, 111]>>

    % A string converted to a char list:
    "hello".to_char_list  % => [104, 101, 108, 108, 111]

    % Strings are UTF-8
    "Arrow ⇧ up".length  % => 10

This difference is important because strings are the only object that needs conversion between Elixir and Erlang. Erlang methods that expect Strings actually expect a binary or a list of characters, so you need to convert any Elixir string before passing them to Erlang.

On the other hand, if you receive a String from an Erlang method you are actually receiving a binary or a list of characters, so you may want to typecast to Elixir's string. Summing up, here are the conversions you need to know:

    % Converting a string_from_erlang to Elixir's String
    String.new string_from_erlang

    % Where string_from_erlang is either a binary:
    <<[104, 101, 108, 108, 111]>>

    % Or a char_list:
    [104, 101, 108, 108, 111]

    % Converting a string_from_elixir to Erlang
    "string_from_elixir".to_bin
    "string_from_elixir".to_char_list

Finally, strings also support interpolation:

    "string #{'with} interpolation"  % => "string with interpolation"
    "1 + 1 = #{1 + 1}"               % => "1 + 1 = 2"

#### Documentation

* <https://github.com/josevalim/elixir/tree/master/lib/string.ex>

### Functions

Functions are an important aspect of Elixir, like in any functional programming language. Functions are created in Elixir with the keywords `->` or `do`:

    my_function = do
      1 + 2
    end

    my_function() % => 3

    another_function = ->
      1 * 2
    end

    another_function() % => 2

Some functions expect arguments:

    my_function = do (x, y)
      x + y
    end

    my_function(1, 2) % => 3

    another_function = -> (x, y)
      x * y
    end

    another_function(1, 2) % => 2

You can also represent functions in one line, without a need for the closing keyword `end`:

    my_function = do (x, y) x + y
    my_function(1, 2) % => 3

    another_function = -> (x, y) x * y
    another_function(1, 2) % => 2

Notice that, whenever using one-line functions, if you need parenthesis inside the expression, you are required to give empty parenthesis arguments, for example:

    % This works as expected:
    my_function = -> 1 + 2
    my_function() % => 3

    % This won't work and it raises a syntax error
    my_function = -> (1 + 2)

    % This works as well:
    my_function = -> () (1 + 2)

In the second case, it is ambiguous if the parenthesis is part of the argument list or the function expressions. This is why you either need to remove parenthesis (as in the first example) or add empty parenthesis (as in the third example). This syntax quickly proves to be very convenient:

    [1,2,3].map(-> (x) x * 2)   % => [2,4,6]

In the example above, we are calling .map passing a function as argument. If we remove the optional parenthesis:

    [1,2,3].map -> (x) x * 2   % => [2,4,6]

Other examples using the multiline syntax:

    [1,2,3].foldl(0, do (x, acc)
      acc + x
    end) % => 6

Again, removing the parenthesis, improves readability:

    [1,2,3].foldl 0, do (x, acc)
      acc + x
    end % => 6

One final note is, while parenthesis are optional for method invocations, function invocations *always* require parenthesis:

    my_function = -> (x, y) x + y

    % This won't work and will raise undefined method my_function
    my_function 1, 2

    % This works
    my_function(1, 2)

#### Documentation

* <https://github.com/josevalim/elixir/tree/master/lib/function.ex>

## Variables and Pattern Matching

Elixir inherits single assignment variables and pattern matching from Erlang. This means there isn't an assignment operator, but rather a match operator. A variable can be assigned just once:

    x = 1
    x = 2  % => Raises a bad match error, because x is already equals to 1

In Erlang/Elixir terms, a variable that was not assigned yet is called *unbound variable*. Let's see more examples:

    % Let's bound the variable x to 'foo
    x = 'foo

    % Now let's match a tuple with other tuple.
    % Since x is already bound, we are comparing x with 'baz and it will fail:
    { x, y } = { 'baz, 'bar }

    % In this case, we compare 'x with 'foo and it matches.
    % Since y is unbound, we assign 'bar to it:
    { x, y } = { 'foo, 'bar }

    x  % => 'foo
    y  % => 'bar

For lists, we can use the same syntax to prepend an item on pattern matching, easily retrieving the head and tail:

    [h|t] = [1,2,3]
    h  % => 1
    t  % => [2,3]

    % Raises an error because h was already assigned to 1 and 1 does not match 2
    [h|t1] = [2,3,4]

Elixir will often complain if you bound a value to a variable but never use it. For instance, imagine that you want to get just the first element of a tuple with three items:

    {x, y, z} = {1, 2, 3}

If you don't use the `y` and `z` variables, Elixir will show you some warnings. For this reason, you could use `_` instead:

    {x, _, _} = {1, 2, 3}

The variable `_` is always unbound:

    _ = 1
    _   % => Raises that variable '_' is unbound

However, sometimes having several occurrences of `_` in the same expression is confusing, so you can do this instead:

    {x, _y, _z} = {1, 2, 3}

The values 2 and 3 will be bound to the variables `_y` and `_z`, but Elixir won't complain if you eventually don't use them.

Keep in mind that the number of expressions allowed in pattern matching are limited. You cannot invoke methods, use interpolated strings, retrieve constants and so on. Therefore, this is invalid:

    1.abs = -1

Finally, pattern matching can also be implemented in methods signatures. Here is the classic Fibonacci example:

    module Math
      def fibonacci(0)
        0
      end

      def fibonacci(1)
        1
      end

      def fibonacci(n)
        fibonacci(n - 1) + fibonacci(n - 2)
      end
    end

    Math.fibonacci(0)   % => 0
    Math.fibonacci(1)   % => 1
    Math.fibonacci(3)   % => 2
    Math.fibonacci(10)  % => 55

We will discuss modules and methods with more details later.

## Operators

Operators can be binary or unary operators:

    (expression) binary_op (expression)
    unary_op (expression)

### Term comparisons

Elixir term comparisons operators are close to Erlang ones, except `!=`, `=!=` and `<=` which maps to Erlang `/=`, `=/=` and `=<`.

<table>
<tr>
  <td><strong>Operator</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td>==</td>
  <td>equal to</td>
</tr>
<tr>
  <td>!=</td>
  <td>not equal to</td>
</tr>
<tr>
  <td>&lt;=</td>
  <td>less than or equal to</td>
</tr>
<tr>
  <td>&lt;</td>
  <td>less than</td>
</tr>
<tr>
  <td>&gt;=</td>
  <td>greater than or equal to</td>
</tr>
<tr>
  <td>&gt;</td>
  <td>greater than</td>
</tr>
<tr>
  <td>=:=</td>
  <td>exactly equal to</td>
</tr>
<tr>
  <td>=!=</td>
  <td>exactly not equal to</td>
</tr>
</table>

As in Erlang, Elixir can order different objects types:

    number < atom < reference < fun < port < pid < tuple < any other object < list < bit string

Lists are compared element by element. Tuples are ordered by size, two tuples with the same size are compared element by element. If one of the compared terms is an integer and the other a float, the integer is first converted into a float, unless the operator is one of `=:=` and `=!=`.

All term comparison operators return a boolean expression.

### Arithmetic operators

<table>
<tr>
  <td><strong>Operator</strong></td>
  <td><strong>Description</strong></td>
  <td><strong>Argument</strong></td>
</tr>
<tr>
  <td>+</td>
  <td>unary +</td>
  <td>number</td>
</tr>
<tr>
  <td>-</td>
  <td>unary -</td>
  <td>number</td>
</tr>
<tr>
  <td>+</td>
  <td></td>
  <td>any object</td>
</tr>
<tr>
  <td>-</td>
  <td></td>
  <td>any object</td>
</tr>
<tr>
  <td>*</td>
  <td></td>
  <td>any object</td>
</tr>
<tr>
  <td>/</td>
  <td>returns a float</td>
  <td>any object</td>
</tr>
<tr>
  <td>div</td>
  <td>returns an integer</td>
  <td>any object</td>
</tr>
<tr>
  <td>rem</td>
  <td>returns an integer</td>
  <td>any object</td>
</tr>
</table>

Except by the two unary operators, all other operators accept any object as parameter. This is because those operators are implemented as methods and their implementation are defined by the object which is receiving the method. For instance, we can concatenate two lists by using the `+` operator:

    [1,2,3] + [4,5,6]  % => [1,2,3,4,5,6]

This is the same as:

    [1,2,3].+([4,5,6]) % => [1,2,3,4,5,6]

Notice however that we cannot add a list with a number:

    [1,2,3] + 1  % => Raises an error

Also, Elixir keeps the same semantics as Erlang in the sense the `/` operator always returns a float when numbers are given as argument. The `div` and `rem` operators are used to deal with integers:

    2 / 1    % => 2.0
    6 div 4  % => 1
    6 rem 4  % => 2

### Bitwise operators

To be implemented/written.

### Short-circuit control operators (to be implemented)

Elixir provides three general boolean operators. They are short-circuit operators and accept any object as argument. They do not return a boolean but the last evaluated object:

<table>
<tr>
  <td><strong>Operator</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td>&&</td>
  <td>and</td>
</tr>
<tr>
  <td>||</td>
  <td>or</td>
</tr>
<tr>
  <td>!</td>
  <td>not</td>
</tr>
</table>

Remember that any object, except `false` and `[]` (empty list), evaluates to `true`:

    ![]          % => true
    !false       % => true
    !true        % => false

    [] && true   % => []
    true && []   % => []
    1 && 2       % => 2

    true || []          % => true
    'atom || 'another   % => 'atom
    false || 'another   % => 'another

    [] && IO.puts("I will never be executed")
    false && IO.puts("I will never be executed")

    1 || IO.puts("I will never be executed")
    true || IO.puts("I will never be executed")

### Strict boolean operators

Elixir provides the following operators to deal strictly with booleans:

<table>
<tr>
  <td><strong>Operator</strong></td>
  <td><strong>Erlang equivalent</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td>and</td>
  <td>and</td>
  <td>Both expressions must return boolean</td>
</tr>
<tr>
  <td>or</td>
  <td>or</td>
  <td>Both expressions must return boolean</td>
</tr>
<tr>
  <td>andalso</td>
  <td>andalso</td>
  <td>First expression must return boolean, short-circuit operator</td>
</tr>
<tr>
  <td>orelse</td>
  <td>orelse</td>
  <td>First expression must return boolean, short-circuit operator</td>
</tr>
<tr>
  <td>not</td>
  <td>not</td>
  <td>Unary operators, expression must be a boolean</td>
</tr>
</table>

### Precedence

Operator precedence in falling priority:

<table>
<tr>
  <td><strong>Operator</strong></td>
  <td><strong>Associativity</strong></td>
</tr>
<tr>
  <td>+ - ! not</td>
  <td>Non associative (unary operators)</td>
</tr>
<tr>
  <td>/ * div rem</td>
  <td>Left</td>
</tr>
<tr>
  <td>== != &lt; &lt;= &gt; &gt;= =:= =!=</td>
  <td>Left</td>
</tr>
<tr>
  <td>and andalso</td>
  <td>Left</td>
</tr>
<tr>
  <td>or orelse</td>
  <td>Left</td>
</tr>
<tr>
  <td>&&</td>
  <td>Left</td>
</tr>
<tr>
  <td>||</td>
  <td>Left</td>
</tr>
</table>

## if/else and case/match

Elixir, differently from Erlang, has a more conventional if/else structure:

    list = [1,2,3]

    if list.include?(4)
      IO.puts "it includes 4"
    elsif list.include?(5)
      IO.puts "it includes 5"
    else
      IO.puts "it does not include 4 or 5"
    end

Everything in Elixir, except false and `[]` (empty list) evaluates to true.

On the other hand, the case/match structure from Elixir is quite similar to Erlang's:

    case {1,2,3}
    match {3,2,x}
      x * 2
    match {1,2,x}
      x * 2
    end

As you can notice, case/match uses pattern matching. If no case expression matches, an error is raised. Elixir also allows an `else` clause in case/match, which is the same as invoking `match _`:

    case {4,5,6}
    match {3,2,x}
      x * 2
    match {1,2,x}
      x * 2
    else
      10
    end

Finally, case/match expressions can be inlined, providing a more compact syntax:

    case {4,5,6}
    match {3,2,x} then x * 2
    match {1,2,x} then x * 2
    else 10
    end

Currently there is no support for guard expressions as in Erlang, although it may be implemented at some point.

## Exceptions

To be written/implemented.

## Strings, Atoms, Regular Expressions, Interpolation and Sigils

In Elixir, we have the following basic types related to Strings:

    % Strings (utf8 by default and represented as binaries)
    "string"
    "string #{'with} interpolation"    % => "string with interpolation"

    % Integer representation of a character
    $a    % => 97
    $b    % => 98
    $\\   % => 92
    $\(   % => 40

    % A string represented as a list of chars (all four expressions below allow interpolation)
    $"string"    % => [115,116, 114, 105, 110, 103]
    $(string)    % => [115,116, 114, 105, 110, 103]
    $[string]    % => [115,116, 114, 105, 110, 103]
    ${string}    % => [115,116, 114, 105, 110, 103]

    % A binary representing the list of chars above
    <<[115,116, 114, 105, 110, 103]>>

    % Erlang Atoms or Ruby Symbols
    'atom
    '"atom with space and interpolation"
    '(atom with space and interpolation)
    '[atom with space and interpolation]
    '{atom with space and interpolation}

Besides these basic types, we also have string sigils. Here is one example:

    % Regular expressions
    %% Without interpolation
    ~r(regexp)
    ~r[regexp]
    ~r{regexp}
    ~r"regexp"

    %% With interpolation
    %% It also accepts [], {} and "" as separators as above
    ~R(regexp #{1 + 1} interpolation)

    %% With regexp operators
    ~r(foo)im

All string sigils follow the same set of rules. They start with a ~ followed by a letter and the string is delimited by a separator. The available separators are (), [], {} and "". If the letter after ~ is lowercased, no interpolation is allowed, if uppercased, interpolation is allowed. A couple more examples:

    % Another way to create strings
    ~q(string without interpolation)
    ~Q{string without interpolation}

    % Another way to create atoms
    ~a"atom without interpolation"
    ~A[atom with interpolation]

    % Another way to create a list of chars
    ~l(string)  % => [115,116, 114, 105, 110, 103]
    ~L{string with interpolation}

    % A list of words (to be implemented)
    ~w(foo bar baz)        % => ["foo", "bar", "baz"]
    ~W{foo #{'bar} baz}    % => ["foo", "bar", "baz"]

#### Documentation

* <https://github.com/josevalim/elixir/tree/master/lib/string.ex>
* <https://github.com/josevalim/elixir/tree/master/lib/atom.ex>
* <https://github.com/josevalim/elixir/tree/master/lib/regexp.ex>

## Invoking Erlang Methods

Invoking Erlang methods with elixir is quite trivial:

    % Accessing the is_atom BIF from Erlang.
    % This is the same as `is_atom(foo)` in Erlang.
    Erlang.is_atom('foo)  % => true

    % Accessing the function delete from module lists.
    % This is the same as `lists:member(1, [1,2,3])` in Erlang.
    Erlang.lists.member(1, [1,2,3]) % => true

Notice that `Erlang` is not a real object in Elixir, but just a proxy that is converted to erlang calls at parse time.

## The Object Model

This section will discuss Elixir's Object Model. Its main aspects are:

* Dynamic Dispatch - when a method is invoked on an object, the object itself determines which code gets executed
* Mixins - an object does not contain methods, all methods are packed into modules that are mixed into objects
* Encapsulation - methods can either be public, protected or private
* Open recursion - Elixir's has a special variable called `self` that allows a method body to invoke another method body of the same object, passing through the ancestors chain
* Reflections - Elixir is able to observe and modify an object structure at runtime

### Why Objects?

Elixir's Object Model focuses on method dispatching. Imagine you have a Person structure with fields name and age generated by an ORM. Retrieving this Person structure, changing its name and saving it would be as follow in Erlang:

    Person = person:find(john),
    ModifiedPerson = person:set(name, john_doe, Person)
    true = person:save(ModifiedPerson)

In Elixir, this would be written as:

    person = Person.find('john)
    modified_person = person.set('name, 'john_doe)
    true = person.save

In Erlang, every time we want to do something with the `Person` structure, we need to explicitly call the module `person` and pass the `Person` structure as parameter.

Elixir is more concise due to method dispatching. Once you create a `person` object, you can invoke methods on it directly and there isn't a need to always pass the own list object as argument. The need for dynamic method dispatching is one of the main reasons for the existence of Elixir and its Object Model.

### How does it work?

In Elixir, everything is an object. Objects carry properties, they don't carry methods. We can define a new object as follow:

    % Define an object Person
    object Person
    end

    % Creates an instance of this object
    Person.new

In Elixir, modules carry behavior, i.e. methods. All modules are objects, but not all objects are modules. We can define a new module `Speak` with a method called `say` that receives a message and prints it out as follow:

    module Speak
      def say(message)
        IO.puts message
      end
    end

The convenience of objects comes when we add modules to objects:

    person = Person.new
    person.say "Hi"          % => Raises no method error

    another_person = Person.new.mixin(Speak)
    another_person.say "Hi"  % => "Hi"

The `mixin` method adds the given module to the *current object* mixins chain. Therefore, every time we invoke a method in this object, it will search if the method exists in one of its mixins. We can retrieve all mixins of an object with the method `__mixins__`:

    another_person.__mixins__  % => ['Speak, 'Object::Methods]

The `Speak` module is the one we added and `Object::Methods` is a module included by default in all objects and is where the `__mixins__` method is implemented.

If every time we create a new `Person` instance, we need to explicitly mix in a module, Elixir wouldn't be much useful. For this reason, we define in the object `Person` that all of its children has a `Speak` module by default. This can be done with the `proto` method:

    object Person
      proto Speak
    end

    Person.new.say "Hi"  % => "Hi"

    Person.__protos__      % => ['Speak, 'Object::Methods]
    Person.new.__mixins__  % => ['Speak, 'Object::Methods]

A `proto` is how an object specifies how its children is going to behave. In other words, a `proto` added to the parent, becomes a `mixin` to the child.

Since everything is an object, the `mixin` method we discussed earlier is also available to the `Person` object:

    module NewBorns
      def create_and_cry
        instance = self.new
        instance.say "whaaaaaaa"
        instance
      end
    end

    object Person
      mixin NewBorns
      proto Speak
    end

    person = Person.create_and_cry  % => "whaaaaaaa"

    Person.__mixins__    % => ['NewBorns, 'Object::Methods]
    Person.__protos__    % => ['Speak,    'Object::Methods]

This wraps the core of Elixir object system. Remember: everything is an object, methods are defined in modules and modules can be either mixed into objects (`mixin`), changing their current behavior, or added as prototype (`proto`), which will define the behavior of all children of that object. Finally, all modules defined as `proto` in the parent, becomes a `mixin` to the child.

On top of that, Elixir provides a better way to organize our code. Let's rewrite our Person object:

    object Person
      module Mixin
        def create_and_cry
          instance = self.new
          instance.say "whaaaaaaa"
          instance
        end
      end

      module Proto
        def say(message)
          IO.puts message
        end
      end

      mixin Person::Mixin
      proto Person::Proto
    end

Instead of defining modules without a namespace, we can define them inside the Person object, avoiding polluting the main namespace and reducing the chance of conflicts.

Finally, since the pattern above is very common, Elixir provides three conveniences to make our code more expressive:

* If a module `Mixin` is defined inside an object, it is automatically added as `mixin`;
* If a module `Proto` is defined inside an object, it is automatically added as `proto`;
* You can also completely skip the `proto` module and define methods as if you were defining methods inside the object. This is a just a syntax convenience. Internally, Elixir will still create a `Proto` module and automatically add it as `proto` to your object.

With these conveniences in mind, let's rewrite our `Person` object once again:

    object Person
      module Mixin
        def create_and_cry
          instance = self.new
          instance.say "whaaaaaaa"
          instance
        end
      end

      def say(message)
        IO.puts message
      end
    end

This is much better! Now let's prove that everything is the same as before:

    Person.__mixins__    % => ['Person::Mixin, 'Object::Methods]
    Person.__protos__    % => ['Person::Proto, 'Object::Methods]

### Instance variables

When creating an object, we sometimes want to define properties specific to that object. For example, a Person may have name and age as properties. This can be done by defining a `constructor`. A `constructor` is a method that receives all arguments given to `new` and should return a dict. The key-values given by the dict can be retrieved using instance variables:

    object Person
      def constructor(name, age)
        { 'name: name, 'age: age }
      end

      def name
        @name
      end

      def age
        @age
      end
    end

    person = Person.new('john, 24)
    person.name % => 'john
    person.age  % => 24

Instance variables can be changed using the `set_ivar` method:

    object Person
      def constructor(name, age)
        { 'name: name, 'age: age }
      end

      def name
        @name
      end

      def age
        @age
      end

      def name(value)
        self.set_ivar('name, value)
      end
    end

    person = Person.name('john, 24)
    another_person = person.name('john_doe)

    person.name % => 'john
    person.age  % => 24

    another_person.name % => 'johh_doe
    another_person.age  % => 24

Notice in the example above that `set_ivar` returns a new object. This is expected because as Erlang structures are immutable, all objects in Elixir are also immutable. Above we can see that the initial person object has not changed at all.

### Advanced: The Object Graph

One final note about the object model is how instantiation works. When you create an instance from `Person`, it annotates that the parent for that instance is the `Person` object. Let's take a look at it:

    person = Person.new
    person.__parent__   % => 'Person

The `Person` object is a direct child from `Object`:

    Person.__parent__  % => `Object`

While all modules are children from `Module` which is a child from `Object`. The object `Object`, has no parent:

    Person::Mixin.__parent__ % => 'Module
    Person::Proto.__parent__ % => 'Module
    Module.__parent__        % => 'Object
    Object.__parent__        % => []

The object `Object` defines `Object::Methods` as `proto`, this is why all objects have this method as their `mixin`. It doesn't matter if you are a child, a grandchild or a grand-grandchild from `Object`, this `mixin` will be available to you. This happens because, in order to calculate all mixins for a given object, Elixir traverses the whole ancestors chain getting all modules defined as `proto` for all parents.

The Object Graph for all these objects can be seen below:

    ---------------    Parent   -----------------
    |    Object   | <---------- |     Module    | <---
    ---------------             -----------------    |
          ^                            ^             |
          |  Parent                    |  Parent     |
          |                            |             |
    ---------------    mixin    -----------------    |
    |   Person    | <---------- | Person::Mixin |    | Parent
    ---------------  _          -----------------    |
          ^         |\__ proto                       |
          |  Parent     \___                         |
          |                 \__                      |
    ---------------            \-----------------    |
    | Person.new  | <---------- | Person::Proto | ---|
    ---------------    mixin    -----------------

Once again, remember:

* Everything is an object;
* Methods are defined in modules. All modules are all objects, but not all objects are modules. Besides, modules cannot have instances. `Person::Mixin` and `Person::Proto` defined above are modules;
* Modules can be either mixed into objects (`mixin`), changing their current behavior...
* Or added as prototype (`proto`), which will define the behavior of all children/instances from that object;
* Finally, all modules defined as `proto` in the parent, becomes a `mixin` to the child.

#### Documentation

* <https://github.com/josevalim/elixir/tree/master/lib/object.ex>

## Modules

In the Object Model section, we have discussed modules and methods. Modules are very close to Erlang modules and it is important to keep that in mind if you are coming from a language like Ruby.

### Local and remote calls

In Erlang, it is very important to make a difference between local calls and remote calls, as they affect how hot code swapping works. You can read this section from [Learn You Some Erlang](http://learnyousomeerlang.com/designing-a-concurrent-application#hot-code-loving) for more information.

Elixir keeps the same semantics as Erlang and makes a difference between local and remove calls. Let's take a look at some examples:

    module Example
      def remote_call
        self.__parent__
      end

      def local_call
        remote_call
      end

      def invalid_local_call
        __parent__
      end
    end

    % Remember the parent for a module is 'Module
    Example.__parent__   % => 'Module
    Example.remote_call  % => 'Module
    Example.local_call   % => 'Module

    % This won't work, in fact, it wouldn't even compile
    Example.invalid_local_call

In Elixir, every time you invoke a method without an explicit receiver (like `self`), you are making a local call. A local call searches for a method *in the current module* and, if no method exists, a compilation error is raised.

This means that, even though all objects have a `__parent__` method, if you call `__parent__` without an explicit receiver inside a method, it will try to find a local method `__parent__` defined inside the current module. In order to really dispatch a method that goes through all mixins for that object, you need to use `self` as explicit receiver.

Note that this rule applies only expressions inside methods. Take this for example:

    module MoreExamples
      mixin Something
    end

Even though `mixin` is a method that comes from `Object`, we can invoke it without an explicit `self`. This doesn't cause conflicts because you can only invoke a method in a module after the module is completely defined. Therefore, this doesn't work:

    module MoreExamples
      def foo
        10
      end

      % Raises no method error
      foo

      % Raises undefined constant MoreExamples
      MoreExamples.foo
    end

Finally, as local calls have the same syntax as variables. If a variable is defined with the same name as method, the variable is given higher preference:

    module AnotherExample
      def some_value
        13
      end

      def value
        some_value = 11
        some_value
      end
    end

    AnotherExample.value  % => 11

### Method Visibility

Now that we know the difference between local and remote calls we can take a better look at method visibility. Elixir provides three different visibilities: *public*, *protected* and *private*. All methods are public by default, this means that a method can be called from anywhere, at any time:

    module Example
      def public_method
        13
      end

      def calling_public_method
        public_method
      end

      def calling_public_method2
        self.public_method
      end
    end

    Example.public_method           % => 13
    Example.calling_public_method   % => 13
    Example.calling_public_method2  % => 13

A public method can be called from another module, as long as it is a remote call:

    module Invoker
      mixin Example

      % This won't work (it won't even compile) because, as we saw
      % previously, it will attempt to call public_method locally.
      % def calling_public_method
      %   public_method
      % end

      def calling_public_method
        self.public_method
      end
    end

    Invoker.public_method          % => 13
    Invoker.calling_public_method  % => 13

A protected method can only be called if the current scope includes the mixin the module belongs to. Some examples:

    module Example
      def calling_protected_method
        public_method
      end

      def calling_protected_method2
        self.public_method
      end

      protected

      def protected_method
        13
      end
    end

    % Here the current scope (self) is Object which doesn't have Example as mixin,
    % so invoking the protected method raises an error.
    Example.protected_method

    % calling_protected_method calls protected_method using a local call.
    % A local call always work, as it means they are in the same module.
    Example.calling_protected_method  % => 13

    % The following also works because calling_protected_method2 and
    % protected_method are defined in the same module.
    Example.calling_protected_method2 % => 13

    module Invoker
      mixin Example

      def calling_protected_method
        % Here the current scope (self) is Invoker, that has the mixin Example
        % So calling Invoker.calling_protected_method at any point will work
        self.protected_method
      end
    end

    Invoker.calling_protected_method  % => 13

Finally, private methods are the ones accessible just through a local call. This means a module cannot access private methods from other modules even after adding them as `mixin` or as `proto`.

    module Example
      def calling_private_method
        private_method
      end

      def calling_private_method2
        self.private_method
      end

      protected

      def private_method
        13
      end
    end

    % Won't work, it is not a local call.
    Example.private_method

    % It works because calling_private_method is doing a local call.
    Example.calling_private_method   % => 13

    % It won't work because calling_private_method2 is not doing a local call.
    Example.calling_private_method2

    module Invoker
      mixin Example

      def calling_private_method
        self.private_method
      end
    end

    % It won't work because private_method is only accessible from
    % local calls from the same module it is defined.
    Invoker.calling_private_method

### Tail call optimization

In the "Variables and Pattern Matching" section above, we have showed a simple Fibonacci example using Pattern Matching in the method signature. However, that example was not properly optimized:

    module Math
      def fibonacci(0)
        0
      end

      def fibonacci(1)
        1
      end

      def fibonacci(n)
        fibonacci(n - 1) + fibonacci(n - 2)
      end
    end

As Erlang, Elixir does tail call optimization (though it only applies to local calls). We can rewrite the fibonacci method with a version that will use tail call optimization like below:

    module OptimizedMath
      def fibonacci(n)
        fibonacci(n, 1, 0)
      end

      def fibonacci(0, _, result)
        result
      end

      def fibonacci(n, next, result)
        fibonacci(n - 1, next + result, next)
      end
    end

    OptimizedMath.fibonacci(0)   % => 0
    OptimizedMath.fibonacci(1)   % => 1
    OptimizedMath.fibonacci(3)   % => 2
    OptimizedMath.fibonacci(10)  % => 55

The third fibonacci function is optimized because the last method it calls is itself. In order to understand the difference between both versions and how tail call optimization works, we recommend reading more about it on the Recursion chapter from [Learn You Some Erlang](http://learnyousomeerlang.com/recursion).

### Retrieving a method as a function

Before proceeding on how to retrieve a method as a function, it is important to notice that, as in Erlang, Elixir's methods are identified by its name **and** arity. Therefore, the `OptimizedMath` module above has only two methods: a `fibonacci` with arity 1 and `fibonnaci` with arity 3. If two methods are defined with same name and arity, they become different clauses for the same method and pattern matching is used in order to specify which method to call. That said, the `Math` module has only one `fibonnaci` method with arity equals to 1 and 3 clauses.

Remaining of this section still needs to be implemented and written.

#### Documentation

* <https://github.com/josevalim/elixir/tree/master/lib/module.ex>

### Behavior and Callbacks

To be written.