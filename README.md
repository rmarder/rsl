# RSL: Robert's Scripting Language

# Documentation

<p><b>RSL IS UNDER ACTIVE DEVELOPMENT SO EVERYTHING HERE CAN BE WRONG OR OUT OF DATE.<br/>
IF IN DOUBT, CHECK THE SOURCE CODE.</b></p>

### [Table of Contents](#)

[1. Syntax](#syntax)<br/>
[2. Standard Library of Functions](#builtins)<br/>
[3. Pre-defined Global Constants](#globals)<br/>
[4. Error handling](#error-handling)<br/>

<a id="syntax"></a>
### 1. Syntax

### Basic types
```bool```, ```string```, ```number``` (double precision float), ```array```, ```map```, ```function```, ```error```

### Operators
```
Math:
+ - * / %

Binary:
^ | & << >>

Logical:
! < > <= >= == != && ||

Assignment:
= += -= *= /= %= ^= |= &= <<= >>=
```

### Defining constants and variables
```javascript
const constant = 2
constant = 1 // fail

var variable = 3
variable = 7 // ok
variable++ // now 8
variable-- // 7 again
++variable // not supported
--variable // not supported
```

## Strings
```javascript
const str1 = "a string"
const str2 = 'also a string'
const str3 = `a template string, it can contain expressions: ${2 + 2}, ${str1}`
```

### Arrays
```javascript
const arr = [1, 2, 3]
arr[0] // -> 1
```

### Maps
```javascript
const map = {"lorem": 1, 'ipsum': 2, dolor: 3}
map.lorem // -> 1, dot is a syntactic sugar for [""]
map["ipsum"] // -> 2
map['dolor'] // -> 3
```

### Conditional statements
```javascript
if (a) {
    // a
} else if (b) {
    // b
} else {
    // c
}
```

### Loops
```javascript
while (true) {
    // body
}

var items = [1, 2, 3]
for (item in items) {
    if (item == 2) {
        break
    } else {
        continue
    }
}

for (var i = 0; i < 10; i++) {
    // body
}
```

### Functions
```javascript
const add_1 = fn(a, b) { return a + b }

fn add_2(a, b) {
    return a + b
}

fn map_items(items, map_fn) {
    const res = []
    for (item in items) {
        append(res, map_fn(item))
    }
    return res
}

map_items([1, 2, 3], fn(x){ return x + 1 })

fn make_person(name) {
    return {
        name: name,
        greet: fn() {
            println(`Hello, I'm ${this.name}`)
        },
    }
}
```

### Errors
```javascript
const err = error("something bad happened")
if (is_error(err)) {
    println(err)
}

fn() {
    recover (e) { // e is a runtime error wrapped in error
        return null
    }
    crash("something bad happened") // crashes are recovered with "recover" statement
}
```

### Modules
```javascript
import "foo" // import "foo.rsl" and load global symbols prefixed with foo::

foo::bar()

import "bar/baz" // import "bar/baz.rsl" and load global symbols prefixed with baz::
baz::foo()
```

### Operator overloading
```javascript
fn vec2(x, y) {
    return {
        x: x,
        y: y,
        __operator_add__: fn(a, b) { return vec2(a.x + b.x, a.y + b.y)},
        __operator_sub__: fn(a, b) { return vec2(a.x - b.x, a.y - b.y)},
        __operator_minus__: fn(a) { return vec2(-a.x, -a.y) },
        __operator_mul__: fn(a, b) {
            if (is_number(a)) {
                return vec2(b.x * a, b.y * a)
            } else if (is_number(b)) {
                return vec2(a.x * b, a.y * b)
            } else {
                return vec2(a.x * b.x, a.y * b.y)
            }
        },
    }
}
```

<a id="builtins"></a>
### 2. Standard Library of Functions

#### General Purpose Functions

`len(string | array | map)` -> `number`
Returns the length of the string, array, or map.

```javascript
  var aStr = "a string"
  var aArr = [1, 2, 3]
  var aMap = { "1": 1, "2": 2 }

  len(aStr) // 8
  len(aArr) // 3
  len(aMap) // 2
```
<br/>

`reverse(array | string)` -> `array | string`
Return a reversed version of a string or array.

```javascript
  var aArr = [1, 2, 3]
  var aStr = "abc"

  reverse(aArr) // [3, 2, 1]
  reverse(aStr) // "cba"
```
<br/>


`println(object, ...)` -> `null`
```javascript
  var aMap = { "a": 1, "b": 2 }

  // appends newline
  println("a") // "a"
  println(aMap) // { "a": 1, "b": 2 }
```
<br/>

`print(object, ...)` -> `null`
```javascript
  var aMap = { "a": 1, "b": 2 }

  println("a") // "a"
  println(aMap) // { "a": 1, "b": 2 }
```
<br/>

#### Array Functions

`first(array)` -> `object`
```javascript
  var aArr = [1, 2, 3]

  first(aArr) // 1
```
<br/>

`last(array)` -> `object`
```javascript
  var aArr = [1, 2, 3]

  last(aArr) // 3
```
<br/>

`rest(array)` -> `array`
```javascript
  var aArr = [1, 2, 3, 4, 5, 6, 7]
  var bArr = []

  rest(aArr) // [2, 3, 4, 5, 6, 7]
  rest(bArr) // null
```
<br/>

`array(number)` -> `array`<br>
`array(number, object)` -> `array`
```javascript
  var aArr = array(3) // [null, null, null]
  var bArr = array(3, "a") // ["a", "a", "a"]
  var cArr = array(3, {}) // [{}, {}, {}]
```
<br/>

`append(array, object)` -> `number`
```javascript
  var aArr = [1]

  append(aArr, 1) // 2
  append(aArr, "a") // 3
```
<br/>

`write_file(string, string)` -> `number`
```javascript
  var path = "./ex.txt"
  var data = "A string in a text file"

  write_file(path, data) // 22
```
<br/>

`read_file(string)` -> `string`
```javascript
  var path = "./ex.txt"

  read_file(path) // "A string in a text file"
```
<br/>

`to_str(string | number | bool | null | map | array)` -> `string`
```javascript
  var aVal = true

  to_str(aVal) // "true"
```
<br/>

`char_to_str(number)` -> `string`
```javascript
  char_to_str('a') // "a"
```
<br/>

`range(number)` -> `array`<br/>
`range(number, number)` -> `array`<br/>
`range(number, number, number)` -> `array`<br/>
```javascript
  var aStart = 2
  var aEnd = 10
  var aStep = 2

  range(aEnd) // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
  range(aStart, aEnd) // [2, 3, 4, 5, 6, 7, 8, 9]
  range(aStart, aEnd, aStep) // [2, 4, 6, 8]
```
<br/>

`keys(map)` -> `array`
```javascript
  var aMap = { "a": 1, "b": 2 }

  keys(aMap) // ["a", "b"]
```
<br/>

`values(map)` -> `array`
```javascript
  var aMap = { "a": 1, "b": 2 }

  values(aMap) // [1, 2]
```
<br/>

`copy(object)` -> `object`
```javascript
  var aMap = { "a": 1, "b": 2 }
  var bMap = null

  bMap = copy(aMap) // { "a": 1, "b": 2 }
```
<br/>

`deep_copy(object)` -> `object`
```javascript
  var aMap = { "a": 1, "b": 2, "c": { "a": 1, "b": 2, "c": { "a": 1 } } }
  var bMap = null

  bMap = deep_copy(aMap) // { "a": 1, "b": 2, "c": { "a": 1, "b": 2, "c": { "a": 1 } } }
```
<br/>

`concat(array | string, object)` -> `number | string`
```javascript
  var aArr = [1, 2]
  var aStr = "ab"
  var bStr = ""

  concat(aArr, [3]) // aArr [1, 2, [3]] -> 3
  concat(aArr, "c") // type error!

  bStr = concat(aStr, "c") // "abc"
  // aStr == "ab"
```
<br/>

`assert(bool)` -> `bool`
```javascript
  assert((1 == 1)) // true
  assert(1 == 2)) // false

  assert((1+2)) // error!
```
<br/>

`remove(array, object)` -> `bool`
```javascript
  var aArr = [1, 2, 3, true]

  remove(aArr, 3) // true
  remove(aArr, 3) // false
```
<br/>

`remove_at(array, number)` -> `bool`
```javascript
  var aArr = [1, 2, 3, true]

  remove_at(aArr, 2) // true
  remove_at(aArr, 2) // true
```
<br/>

`error(string | null)` -> `error`
```javascript
  error("an error")
  error()
```
<br/>

`random(number, number)` -> `number`
```javascript
  random(1, 5)
  random(10, 2) // error!
```
<br/>


#### Type Checks
---

`is_string(object)` -> `bool`
<br/>

`is_array(object)` -> `bool`
<br/>

`is_map(object)` -> `bool`
<br/>

`is_number(obejct)` -> `bool`
<br/>

`is_bool(object)` -> `bool`
<br/>

`is_null(object)` -> `bool`
<br/>

`is_function(object)` -> `bool`
<br/>

`is_external(object)` -> `bool`
<br/>

`is_error(object)` -> `bool`
<br/>


#### Math
---

`sqrt(number)` -> `number`
<br/>

`pow(number, number)` -> `number`
```javascript
  pow(2, 2) // 4
```

`sin(number)` -> `number`
<br/>

`cos(number)` -> `number`
<br/>

`tan(number)` -> `number`
<br/>

`log(number)` -> `number`
<br/>

`ceil(number)` -> `number`
<br/>

`floor(number)` -> `number`
<br/>

`abs(number)` -> `number`
<br/>

<a id="globals"></a>
### 3. Global Constants

#### I/O File Descriptors

`STDIN` -> `number`
Points to the standard input file descriptor.

`STDOUT` -> `number`
Points to the standard output file descriptor.

`STDERR` -> `number`
Points to the standard error file descriptor.

#### Environment

`HTTP` -> `map`
If we are running as a CGI program, contains the CGI environment as parsed from STDIN.

`ENV` -> `map` 
Contains the entire execution environment passed from the operating system.

<a id="error-handling"></a>
### 4. Error handling

During program execution runtime errors can occur (for instance if wrong number of arguments is passed to a function). They can be handled with ```recover``` statement that works similar to ```catch``` statement in other languages with few important differences:
* It needs to be declard <b>before</b> a runtime error it handles might occur
* It cannot be nested
* It's only valid within functions and <b>must</b> end with a return statement

For instance, let's say there's a function ```foo()``` that calls ```bar()``` and then ```baz()```.

```javascript
fn foo() {
    recover (e) { // e is a runtime error wrapped in error
        println(e)
        return 0
    }
    bar() // if it fails foo() will return 0

    recover (e) {
        println(e)
        return 1
    }
    baz() // if it fails foo() will return 1

    return 2
}
```

Runtime crashes can be caused by calling ```crash("msg")``` function, however, if it's something expected that can be handled by caller, it's better to return an ```error``` value.

```javascript
const err = error("something bad happened")
if (is_error(err)) {
    println(err)
}
```
