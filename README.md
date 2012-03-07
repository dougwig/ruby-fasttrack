# Ruby, a high-speed introduction 

This intro is a high-speed introduction for experienced Python programmers.
This introduction applies only to Ruby 1.9.3 and above.

## Indenting

The Ruby community likes 2 space indentation.  It actually says this in the
Programming Ruby book!

Also, Ruby is not an indentation-sensitive language.

## Facilities

Ruby has built-in code-coverage, debugger, profiler and benchmarking tools
(the last for targeted profiling).

    ruby -f debug ...
    ruby -f profile ...
    ruby -f coverage ...

## Basic types

### Common keywords

    Ruby  | Python 
    --------------
    true  | True   
    false | False  
    nil   | None   

### Strings

Simple strings are as in Python

    "Interpolation can occur here (see below)"
    'No interpolation happens'
    
Multi-line strings are introduced with `%q<delimiter>` or `%Q<delimiter>`.
The `%Q` variant allows interpolation.  Also, just `%<del>` is the same as
`%Q<del>`

    %/This is a
    multi-line
    interpolable string/

You can use bash-style "here" documents as well

    puts <<EOF, <<EOF2
    This is yet another multi-line string.
    EOF
    And another!
    EOF2
    
## Arrays

Ruby's "arrays" are what we think of as lists.  They are variable length and
can contain values of any type

    x = [1, "foo", 2.0]
    
    x[1] == 'foo'

    x[3] == nil
    x << "hello"                # Append element to the end of 'x'
    x[3] == "hello"
    
You can perform set operations on arrays

    [1, 2, 3, 4] + [2, 3, 5]                     # Concatenation
    [1, 2, 3, 4] & [2, 3, 5] == [2, 3]           # Intersection
    [1, 2, 3, 4] . [2, 3, 5] == [1, 4]           # Drop elems also in 2nd
    [1, 2, 3, 4] | [2, 3, 5] == [1, 2, 3, 4, 5]  # Union

And many other functions (this is just a few)

    [1, 2, 3].size == 3
    [1, 2, 3].include? 1 == true
    [3, 2, 1].sort == [1, 2, 3]
    [1, 2, 3].reverse == [3, 2, 1]
    [1, nil, 2, 3].compact == [1, 2, 3]
    [[1], [2, 3]].flatten == [1, 2, 3]
    [1, 2, 2, 3].uniq == [1, 2, 3]

    [1, 2, 3].zip([4, 5, 6]) == [[1, 4], [2, 5], [3, 6]]
    [[1, 4], [2, 5], [3, 6]].transpose = [[1, 2, 3], [4, 5, 6]]
    
    # This use of closure syntax is described later
    [1, 2, 3].map { |x| x + 2 } == [3, 4, 5]
    [1, 2, 3].select { |x| x == 2 } == [2]
    [1, 2, 3].reject { |x| x == 2 } == [1, 3]

## Hashes

Hashes are very similar to Python dictionaries

    x = {
      'foo' => 'bar',
      'baz' => 'bow',
    }

    puts x['foo']                 # prints 'bar'
    
Any object can be a key, including a **symbol**.  Symbols mean the exact same
thing they do in Lisp, except that Ruby's `:foo` is equivalent to Lisp's
`'foo`, and not Lisp's own `:foo`

    x = {
      :foo => 'bar',
      :baz => 'bow',
    }

    puts x[:foo]                  # prints 'bar'

As a shortcut, you change things around for this special case

    x = {
      foo: 'bar',
      baz: 'bow',
    }

    puts x[:foo]                  # prints 'bar'

Common hash methods

    x.has_key? :foo == true
    x.delete :foo

    x.each { |key, value| ... }
    x.each_key { |key| ... }
    x.keys.sort.each { |key| ... }

## Printing

`puts` prints with a newline

    puts "Hello"
    
`print` does not emit a newline

    print "Hello\n"
    
`printf` is just like it sounds

    printf "Hello %s\n", "world"
   
`p` prints the Ruby representation of an object

    p "Hello"         # prints '"Hello"'

The `inspect` method is useful for debugging

    print "ugly: #{[1,2,3]}, pretty: #{[1,2,3].inspect}"
   
## String interpolation

You can interpolate values Python-style

    world = "world"
    "Hello %s" % world
    
Or you can do it Ruby-style

    "Hello #world"
    
You can interpolate any expression in Ruby-style

    "Hello #{1 + 3}"      # => Hello 4

## Branching

If

    if true
      puts "This is true"
    end

    if true then puts "This is true" end
    
If/else

    if true
      puts "This is true"
    elsif false
      puts "This is false"
    else
      puts "What is this?"
    end
    
Unless

    unless false
      puts "This is true"
    end
    
Case/when

    case x
    when true
      puts "This is true"
    when false
      puts "This is false"
    else
      puts "What is this?"
    end

    case x
    when true  then puts "This is true"
    when false then puts "This is false"
    end
    
Multi-value case

    case x
    when true, false                # This when matches two values
      puts "This is either true or false"
    else
      puts "What is this?"
    end
    
No-value case

    case
    when x == true                  # Matches on predicate, not a value
      puts "x is true"              # much like Lisp's `cond'
    when y == false
      puts "y is false"
    end
    
Case with ranges (described in detail later)

    case x
    when 1...10
      puts "Between 1 and 9.999999"
    when 10...100
      puts "Between 10 and 99.999999"
    when 100...1000
      puts "Between 100 and 999.999999"
    end
    
You can pass case to a function, instead of the other way around

    puts case x
    when 1...10
      "Between 1 and 9.999999"
    when 10...100
      "Between 10 and 99.999999"
    when 100...1000
      "Between 100 and 999.999999"
    end
    
## Looping

While

    i = 0
    while i < 10
      i += 1
    end
    
Until
    
    i = 0
    until i == 10
      i += 1
    end
    
For loops are just like Python

    for i in [1, 2, 3]
      puts i
    end
    
Although it is more idiomatic to use `each` with a closure (more later)
    
    [1, 2, 3].each { |i| puts i }
    
## Suffix-wise branching and looping

Most of the branching and looping constructs can be used as expression
modifiers

    puts "Hello" if true
    puts "Hello" unless false
    puts "Hello" while true
    puts "Hello" until false

## break/next/redo

For loops can use `break/next/redo`.  `redo` restarts the loop's body without
moving to the next element.

    for i in 0..10          # Iterate from 0 up to (and including) 10
      puts i
      if i == 5
        redo                # causes an infinite loop, i always == 5
      elsif i == 6
        break               # exit the loop
      elsif i == 7
        next                # continue the loop
      end
    end
    
## Ranges

Inclusive ranges

    for i in 1..10             # from 1 to 10, inclusive
      puts i
    done
    
Exclusive ranges

    for i in 1...10            # from 1 to 10, but not including 10
      puts i                   # the same as 1..9 for integers
    end                        # the same as 1..9.999999999... for floats
                      
Logical ranges (as in Perl) begin when the first expression is true, and end
once the last expression is true

    open('/etc/passwd').each do |line|
      if line =~ /foo/ .. line =~ /bar/
        print "I'm between foo and bar matching lines, inclusively"
      end
    end

## Functions

Functions are almost the same as in Python, except the final statement implies
a `return`

    def return_ten
      10
    end

Parentheses are optional when calling Ruby functions.

Python:

    'Hello'.lower  
    <built-in method lower of str object at 0x1081dbc90>  
	
Ruby:

    'Hello'.downcase  
    "hello"  

You can be also be literal about (for example, to return from a different
point in the function)

    def return_ten
      return 10
    end
    
Function definitions and calls don't need parentheses, except to disambiguate

    def foo(x)
      puts x
      "sent"
    end
    
    foo(foo "Hello")       # Prints "hello\nsent\n"
    foo foo("Hello")       # Prints "hello\nsent\n"
    
### Array parameters

Functions can gather their arguments into an array, just as in Python

    def foo(*x)
      x.each { |x| puts x }
    end
    
    foo 1, 2, 3
    
You can also do the reverse when calling functions

    def foo(x, y, z)
      puts x, y, z
    end
    
    foo *[1, 2, 3]       # Converts the array into an argument list
    
**NOTE**: Using `*` to convert arrays into argument lists can be done anywhere
in Ruby, not just in function calls

    ary = [1, 2, 3]
    
    [ary]  == [[1, 2, 3]]
    [*ary] == [1, 2, 3]

### Named parameters (sort of)

Ruby sort-of supports both positional and named parameters

    def foo(name, gender, details)
      puts "My name is #name"
      puts "My gender is #gender"
      puts "My age is #{details[:age]}"
    end
    
    foo "John", "Male", age: 39
    
What Ruby is doing here is passing `age: 39` as a hash, and the function is
all the remaining arguments as a hash.  But I can't use `name:` for the first
argument, and I can't prevent someone from passing a non-hash value as the
third parameter.  *This feature is an afterthought*, and new in Ruby 1.9.

## Regular expressions

As in Perl, regexp objects are specified with `/regexp/`.  They are part of
the core language in Ruby, which I love

    regex = /foo/
    
    "foobar" =~ /foo/                # Does it match?
    "foobar" !~ /foo/                # Does it not match?

    "(foo)bar" !~ /foo/              # Matches are captured in special globals
    puts $1

    puts open('/etc/passwd').grep(/johnw/)

Combine this with Ruby's case statement, and Python can go suck it.

    def some_function(x)
      case x
      when /foo/, /bar/
        puts "It's a foo or a bar!"
      when /([A-Za-z]+)/
        puts "It's a #{$1}!"    # #{expr} interpolates that expr
      end
    end

    some_function "Hello"       # It's a Hello!
    some_function "foo"         # It's a foo or a bar!
    
**NOTE**: The quick form of case uses `then`:

      case x
      when /foo/ then "It's a foo"
      when /bar/ then "It's a bar"
      end
      
## Iteration

**Iterators** are closures that get called repeatedly, for example to iterate
over an array:

    [1, 2, 3].each { |i| puts i }
    
Multiple-line closures use `do/end` notation.  The `|i|` is the argument list.
    
    array.each do |i|
      puts i
      puts i
    end
    
Closures are used extensively throughout Ruby.  For example, to print every
line in a file containing root:

    open('/etc/passwd').grep(/root/) do |line|
      puts line
    end
    
The reason we didn't need to say `grep().each` (which would have worked) is
that grep itself accepts a closure.
    
Lastly, since calling `puts` on an array prints out each of its members
followed by a newline, we can just say:

    puts open('/etc/passwd').grep(/root/)
    
### Other Enumerables
    
There are tons of methods that take closures or return Enumerable objects.

    3.times do
      print "Ho! "              # prints "Ho! Ho! Ho! "
    end
    
    0.upto(9) do |x|
      print x, " "              # prints "0 1 2 3 4 5 6 7 8 9 "
    end
    
Lastly, you can iterate over collections using the Python-esque `for` syntax:

    for i in array
      puts i
    end
    
## Closures

Closures can be passed to any function, after its arguments.  The called
function can invoke the closure with `yield`.  `yield` both takes and returns
values:

    def foo(x)
      yield x
    end
    
    foo("Hello") { |arg| puts arg }     # Don't know why parens are needed
    
    # Equivalent to
    foo "Hello" do |arg| 
      puts arg
    end

This "closure after the arguments" can be captured as a value by ending the
argument list with `&name`.  You can call that closure with its `call` method:

    def foo_taking_block(x, &block)
      block.call(x)
    end
    
    foo_taking_block "Hello" do |arg| 
      puts arg
    end

Lastly, you can pass closures as regular arguments, but the caller has to
specify it's a lambda value in order to avoid the "after the arguments"
sugaring.

    def foo_taking_lambda(x, block)
      block.call(x)
    end
    
    foo_taking_lambda("Hello", lambda do |arg| 
      puts arg
    end)

    # Ruby 1.9 syntax for lambdas
    foo_taking_lambda("Hello", -> arg { puts arg })

## Classes

Instance variables are prefixed with "@", not "self."

    class Foo
      def bar
        @n = 2
      end
    end

Private methods are private, and not just a silly leading underscore naming convention.

    class Foo
    private
      def suck_it_python
        "can't call me from outside"
      end
    end

Ruby has class methods, which do not require object instantiation.

    class Foo
      def bar
        @n = 2
      end
    end
    Foo.new.bar

    class Bar
      def self.foo
        'hiya'
      end
    end
    Bar.foo

Classes can be modified on the fly, not just via inheritance.  This can quickly become an
unreadable mess, so save it for extending/adding to classes via modules.

    class String
      def foobar
        'hello world'
      end
    end
    "hello".foobar
    "hello world"

## Working with directories

Iterate over all directory entries (including . and ..).  entry is the name of
the directory entry, not its full path.

    Dir.foreach("/tmp") do |entry|
        puts entry
    end

Iterate over just those directory entries matching a glob pattern.

    Dir.chdir("/tmp") do
      Dir.glob("*") do |entry|
        puts entry
      end
    end

If you use `Dir.glob("/tmp/*")`, instead of `chdir + glob` the results are
full pathnames.

Ruby's globbing syntax can match directories recursively with `**`

    Dir.glob("/tmp/**/*") do |entry|
      puts entry
    end

The same result can be had with `Find.find`

    require 'find'

    Find.find("/tmp/") do |entry|
      puts entry
    end

`Find.prune` causes directories not to be traversed while finding

    require 'find'

    Find.find("/tmp/") do |entry|
      if File.basename(path)[0] == ?.    # Does the dir begin with .?
        Find.prune                       # Don't descend into it
      end
    end
