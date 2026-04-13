<!-- omit from toc -->
# C++ Contribution Code Standards
###### Revision from 2026-04-13
---

This document explains standards that the C++ code you contribute must adhere to. Enforcement of specific points is up to the reviewer.

<!-- omit from toc -->
# Table of Contents
- [1. Environment Configuration](#1-environment-configuration)
  - [1.1 Files](#11-files)
  - [1.2 Tooling](#12-tooling)
  - [1.3 Project Structure](#13-project-structure)
    - [Geode Mods](#geode-mods)
- [2. Naming Conventions](#2-naming-conventions)
  - [2.1 General Notes](#21-general-notes)
  - [2.2 Variables](#22-variables)
  - [2.3 Misc Entities](#23-misc-entities)
  - [2.4 Class Members](#24-class-members)
  - [2.5 Files](#25-files)
  - [2.6 CMake](#26-cmake)
  - [2.7 Comments](#27-comments)
  - [2.8 Git Commits](#28-git-commits)
  - [Geode Mods](#geode-mods-1)
- [3. File Structure and Whitespace](#3-file-structure-and-whitespace)
  - [3.1 General Notes](#31-general-notes)
  - [3.2 Header File Structure](#32-header-file-structure)
  - [3.3 Source File Structure](#33-source-file-structure)
- [4. Structs/Classes/Unions](#4-structsclassesunions)
  - [4.1 General Notes](#41-general-notes)
  - [4.2 Structure](#42-structure)
    - [4.2.1 Structs](#421-structs)
    - [4.2.2 Classes](#422-classes)
    - [4.2.3 Unions](#423-unions)
  - [4.3 Alignment](#43-alignment)
- [5. General Codestyle](#5-general-codestyle)
  - [5.1 Types](#51-types)
    - [5.1.1 Integers](#511-integers)
    - [5.1.2 Enums](#512-enums)
    - [5.1.3 Floating Point](#513-floating-point)
    - [5.1.4 `auto`](#514-auto)
    - [5.1.5 `restrict`](#515-restrict)
  - [5.2 Keywords and Syntax Elements Ordering](#52-keywords-and-syntax-elements-ordering)
  - [5.3 Operators](#53-operators)
  - [5.4 Braces](#54-braces)
  - [5.5 Statement Separation](#55-statement-separation)
  - [5.6 Control Flow](#56-control-flow)
    - [5.6.1 General Notes](#561-general-notes)
    - [5.6.2 `do while`](#562-do-while)
    - [5.6.3 `for`](#563-for)
    - [5.6.4 `if`](#564-if)
    - [5.6.5 `switch`](#565-switch)
    - [5.6.6 `goto`](#566-goto)
    - [5.6.7 `try...catch`](#567-trycatch)
  - [5.7 Functions](#57-functions)
    - [Geode Mods](#geode-mods-2)
  - [5.8 Templates](#58-templates)
  - [5.9 Parameter Packs](#59-parameter-packs)
  - [5.10 Lambdas](#510-lambdas)
  - [5.11 Macros](#511-macros)
  - [5.12 Casts](#512-casts)
  - [5.13 Namespaces](#513-namespaces)
- [6. Wrapping](#6-wrapping)
  - [6.1 General Notes](#61-general-notes)
  - [6.2 Functions](#62-functions)
    - [6.2.1 General Notes](#621-general-notes)
    - [6.2.2 Constructors](#622-constructors)
  - [6.3 Control Flow Elements](#63-control-flow-elements)
  - [6.4 Operators](#64-operators)
  - [6.5 Templates](#65-templates)
- [7. Final Notes](#7-final-notes)


# 1. Environment Configuration
## 1.1 Files
All indentation should be in _tabs_ (`\t`, ASCII `0x9`). This can be semi-automatically handled by most IDEs/text editors

A single tab should be visually equal to 4 spaces

Ligatures aren't banned, but it's recommended to keep them disabled to maintain monospace alignment

C++ files should have _no newline_ at the end

Newline format should be automatically configured by Git via the `core.autocrlf` setting depending on your platform. Set it to `true` if you're on Windows and `input` if you're on a Unix-like system (Linux/macOS)

C++ source files should have the `.cpp` extension, while C++ header files should have the `.hpp` extension. C++ module files should have the `.cppm` extension

## 1.2 Tooling
Normally, all projects are built with **CMake**, unless specified otherwise. For CMake projects the artifacts directory should be named `build/` and added to `.gitignore` (which likely already includes it). The compiler of choice is **clang**, with the LSP of choice being **clangd** with _clang-tidy_. If your environment produces artifacts other than `.cache/`, add them to `.gitignore` or use a system-wide `.gitignore` for these artifacts

## 1.3 Project Structure
All projects **must** have at least `README.md`, `CONTRIBUTING.md` and `LICENSE`. Extra meta files can be added per project

`CMakeLists.txt` should recursively glob source files by directories (e.g. `src/*.cpp`)

The following is an example structure for a _standalone_ project:
```
build/
cmake/
  module.cmake
src/
  classes/
    class1.hpp
    class1.cpp
  utils/
    macros/
      macros1.hpp
    utility1.hpp
    utility1.cpp
  main.cpp
.gitignore
CMakeLists.txt
CONTRIBUTION.md
LICENSE
README.md
```

The following is an example structure for a _library_ project:
```
build/
cmake/
  module.cmake
include/
  project-name/
    classes/
      class1.hpp
      classes.hpp
    utils/
      utility1.hpp
      utils.hpp
    project-name.hpp
src/
  classes/
    class1.cpp
  utils/
    utility1.cpp
standalone/
    utils/
      standalone-utility1.cpp
      standalone-utility1.hpp
    main.cpp
.gitignore
CMakeLists.txt
CONTRIBUTION.md
LICENSE
README.md
```

`standalone/` is optional if a library provides a standalone tool version. `include` contains API headers and is a `PUBLIC` include directory in `CMakeLists.txt`. `project-name.hpp` is a general "include everything" header that includes all category headers (`classes/classes.hpp`, `utils/utils.hpp`, ...), potentially silencing clang-tidy warnings for them with `// IWYU pragma: keep`

### Geode Mods
For Geode mods the structure of `src/` varies slightly with more verbose sections:
```
src/
  hooks/
    MenuLayer.cpp
  types/
    classes/
    layers/
    popups/
  utils/
```

_These are purely examples, actual directories can vary between projects and are fine as long as they follow the general idea and make sense_


# 2. Naming Conventions
## 2.1 General Notes
`const(expr)`-ness does _not_ affect naming

Short forms like `str` for string, `spr` for sprite, `btn` for button are permitted in local variable names but _not in function names_

Swear words in variable names/comments/etc. are allowed, but please focus on naming code entities logically and instead share your opinion in commit messages/comments

## 2.2 Variables
Local variables - `camelCase`

Static to _source file_ variables - `s_camelCase`

Static to _function_ variables - `camelCase`

Global variables are **discouraged entirely**

Template variables - `camelCase_v`

Variable names generally should be _descriptive_. In simple logic cases, one/two letter variables names are permitted (e.g. `i` and `j` could be used as generic iteration variables; `c` could be used as a `char` variable for string iteration; `e` could be used as an exception name in a `try...catch` block)

## 2.3 Misc Entities
Macros - `PROJECT_NAME_UPPER_SNAKE_CASE`

_All_ functions - `camelCase`

Structs/Classes/Unions - `PascalCase`

Enums/Class enums - `PascalCase`

Enum members - `PascalCase` **(unscoped enums are discouraged unless scoped externally)**

Class enum members - `PascalCase`, `UPPER_SNAKE_CASE` if "internal" (e.g. a `COUNT` member)

Namespaces - `snake_case`, but generally names should be kept _relatively short_ and at _one word_

Concepts - `PascalCase`

Modules - `snake_case`

Labels - `camelCase`

`using` aliases - `PascalCase` _with no `_t` suffix_, **aside from template aliases similar to `std::type_identity_t`**

`typedef` aliases are **discouraged entirely**

Template parameters:
- `T` for a single, generic (possibly constrained) type
  - Semantic, _short_ names in `PascalCase` for more than one type
- `N` for integer NTTPs
  - Semantic, _short_ names in `PascalCase` OR fitting letters (e.g. `A` for `std::meta::info`) for other NTTPs

## 2.4 Class Members
Member variables - `m_camelCase`

Static variables - `s_camelCase`

Destructors - `~ClassName`

## 2.5 Files
Class files - according to the class name (e.g. a file containing the definition of a `HelperClass` class should be named `HelperClass.cpp`)

General files - `kebab-case.ext`

## 2.6 CMake
Variables - `UPPER_SNAKE_CASE`

Modules and extra files - `kebab-case.cmake` in `cmake/`

## 2.7 Comments
Comments should be in American English, fully _lowercase_, potentially informal and have no full punctuation. The primary purpose of this is to keep the general "felling" of the codebase approachable and avoid potential AI accusations in projects where it's undesired

## 2.8 Git Commits
Commit messages should have proper capitalization (but no ending `.`), be formal, descriptive and wrap file names and code entities with grave accent characters (`` ` ``)

If you're really tired they can follow comments' style

## Geode Mods
Hooked classes - `HClassName` (e.g. `HMenuLayer` as a hooked `MenuLayer`)


# 3. File Structure and Whitespace
## 3.1 General Notes
Within their categories, headers should preferably be ordered _by their usage in the code_ (e.g. if you're using `<string_view>` a few lines before you use `<span>`, `#include <string_view>` should come first)

`using namespace` statements should be ordered in _alphabetical order_ of namespaces

More detailed whitespace and indentation rules regarding specifically functions, namespaces, classes etc. are described further in their respective sections

## 3.2 Header File Structure
- `#pragma once`
- _Empty line_
- Project headers (included with `""`, added globally via `target_include_directories` in CMakeLists.txt)
- _Empty line_
- Framework's headers
- _Empty line_
- 3rd party headers
- _Empty line_
- Standard library headers
- _Empty line [Geode mods, optional]_
- _`Geode/modify` headers [optional]_
- _2 empty lines_
- Code
- _Empty line_ (between elements of a file, e.g. functions)

If a file has multiple major elements (e.g. 2 separate classes), they should be separated with _2 empty lines_

Example (`helpers.hpp`):
```c++
#pragma once

#include "utils.hpp"

#include <fmt/format.h>

#include <string_view>
#include <span>


class Helper1 final {
    // ...
};


class Helper2 final {
    // ...
}
```

## 3.3 Source File Structure
- Header for implementation _[optional, only needed if the source file implements a header]_
- _Empty line [optional]_
- Project headers (included with `""`, added globally via `target_include_directories` in CMakeLists.txt)
- _Empty line_
- Framework's headers
- _Empty line_
- 3rd party headers
- _Empty line_
- Standard library headers
- _Empty line [Geode mods, optional]_
- _`Geode/modify` headers [optional]_
- _Empty line_
- `using namespace` directives **for project's/main framework's** namespace, if needed. `using namespace std` is **banned entirely**
- _2 empty lines_
- Code
- _Empty line_ (between elements of a file, e.g. functions)

If a file has multiple major elements (e.g. 2 separate classes), they should be separated with _2 empty lines_

Example (`helpers.cpp`):
```c++
#include "helpers.hpp"

#include "Class1.hpp"

#include <vector>

using namespace example;


void Helper1::foo() noexcept {
    // ...
}

void Helper1::bar() noexcept {
    // ...
}


void Helper2::baz() noexcept {
    // ...
}
```


# 4. Structs/Classes/Unions
## 4.1 General Notes
Structs should be used for simple general purpose aggregates (even if the structure in question isn't an aggregate by definition), pimpl `Impl` structs and data-focused types. May have custom constructors, a custom/RAII destructor and, potentially, small helper getters/utility functions. Members follow `camelCase`

Classes should be used for Object-Oriented Programming, particularly encapsulation focused objects. This means classes can have robust member functions, non-`public` members, etc. Members follow dedicated naming conventions, described in [2.4 Class Members](#24-class-members)

Unions should rarely be used due to type-safe `std::variant` existing. Otherwise they follow rules similar to structs. **Union-based type punning is heavily discouraged due to being UB with better alternatives!**

_All_ of these types must be marked `final` if applicable

In member functions, _member function calls should have an explicit `this` pointer_ (unless it's a base-class qualified **initialization delegation**, such as `Base::init()`), while member variable access should _not_ have an explicit `this`

## 4.2 Structure
### 4.2.1 Structs
Should have raw members, optionally with a custom constructor(s) and/or destructor. Constructor(s)/destructor should be put on top of members with one line of separation and use member initializer lists. Inline initializers are allowed

Example:
```c++
struct Example1 final {
    Example1(std::uint64_t* pointer, std::int16_t number) : pointer(pointer), number(number) {
        std::println("Constructed with {} and {}", pointer, number);
    }

    ~Example() {
        std::println("Destructed");
    }

    std::string string{};
    std::uint64_t* pointer;
    std::int16_t number;
};
```

### 4.2.2 Classes
Can have anything OOP related.

The order for access sections is as follows:
- Pimpl struct + `std::unique_ptr` declaration _(under no access specifiers)_
- _Empty line_
- Friend classes _(under no access specifiers)_
- _Empty line_
- Friend functions _(under no access specifiers)_
- _Empty line_
- Public nested entities
- Protected nested entities
- Private nested entities
- Constructors/Assignment operators/Destructor
- Public methods
- Protected methods
- Private methods
- Public member variables
- Protected member variables
- Private member variables

Access specifiers indentation should be at the `class` keyword level. Access sections must be _separated with a newline_. Specific access sections may be occasionally interrupted with other access sections for better readability (see `init` in the example). Beginning of member variable sections must be marked with a `// Fields` comment. Inline initializers are allowed and preferred for _static_ members over definition in a TU, but for general member variables initialization via a constructor is preferred. Static functions should appear before non-static ones in their respective access sections. Static members should appear before other members and be separated from them with a newline

Copy/move constructors/assignment operators must be deleted if unneeded and mentioned in the following order (along with constructors and a destructor):
- Basic constructor(s)
- Copy constructor
- Copy assignment operator
- Move constructor
- Move assignment operator
- Destructor

If some of these functions are being mass deleted they may have no newline between them

Virtual function overrides should always be marked `override` or `override final` respectively

Example:
```c++
class Example2 final : public Base {
    friend void foo();

public:
    enum class Color : std::uint8_t {
        Red,
        Green,
        Blue
    };

private:
    Example2() {
        ++s_instances;
    }

public:
    Example2(Example2 const&) = delete;
    Example2& operator=(Example2 const&) = delete;
    Example2(Example2&&) = delete;
    Example2& operator=(Example2&&) = delete;

    ~Example2() override {
        --s_instances;
    }

public:
    static Example2* create(Color color) {
        auto ret = new Example2;

        if (ret->init(color)) {
            ret->postInit();
            return ret;
        }

        delete ret;
        return nullptr;
    }

private:
    bool init(Color color) noexcept {
        if (!Base::init())
            return false;

        m_color = color;
        m_string = this->colorToString(color);

        return true;
    }

public:
    [[nodiscard]] std::string_view getString() const noexcept {
        return { m_string };
    }

private:
    [[nodiscard]] std::string colorToString(Color color) const noexcept {
        switch (color) {
            case Color::Red:
                return "Red";

            case Color::Green:
                return "Green";

            case Color::Blue:
                return "Blue";

            default:
                return "UNDEFINED";
        }
    }

// Fields
private:
    static inline std::uint64_t s_instances = 0u;

    std::string m_string;
    Color m_color;
};
```

### 4.2.3 Unions
Rules for unions are similar to structs, see [4.2.1 Structs](#421-structs)

## 4.3 Alignment
**Both structs and classes MUST have their layout optimized for type alignment!** As a first step try reordering members in the descending order of their sizes, then account for potential stricter alignment specified with `alignas`. If unsure check the object layout for holes with tools like `pahole`

Consider using `alignas` to optimize cache locality if the type is close enough to applicable sizes and is vastly used in arrays


# 5. General Codestyle
## 5.1 Types
### 5.1.1 Integers
_All_ integer types should be referred to by their `std::` aliases from `<cstdint>` (e.g. `std::uint8_t`, `std::int32_t`, etc.). Exceptions to this rules include generic integer representations for metaprogramming (e.g. `std::convertible_to<int>`)

When using integer types, the smallest one applicable should be used (e.g. when iterating over a static array that's very unlikely to grow over 10 elements in size, `std::uint8_t` should be used)

All literals for integers should have the following suffixes (unless other suffixes are required):
- No suffix for any _signed_ integer type
- `u` for any _unsigned_ integer type
- `z` for `std::size_t` if working on a C++23+ project

These also apply to parameters (e.g. `std::array` size NTTP)

### 5.1.2 Enums
Enums should have their size limited to the lowest applicable size (most commonly `std::uint8_t`)

Every new value should be on its own line

"Internal" values should be separated from general ones with a newline

```c++
enum class Example : std::uint8_t {
    Value1,
    Value2,
    Value3,

    COUNT
};
```

### 5.1.3 Floating Point
For floating point numbers the smallest applicable type should be used.

_All_ `float` literals should have the `f` suffix

```c++
float a = 0.f;
double b = 0.;
float c = 1.2f;
double d = 3.4;
```

### 5.1.4 `auto`
The `auto` keyword use is encouraged for pointers and classes but should generally be avoided for simple integers or during iteration of simple objects like `std::string`s (here in favor of simply `char`)

Pointers should _not_ be added to `auto` unless extra specification is required (like `auto const*`)

### 5.1.5 `restrict`
Compiler-specific `restrict` alternatives (i.e. mainly `__restrict__` and `__restrict`) should be provided as a compiler-agnostic macro and should be used whenever applicable. _Please make sure to analyze your code for strict aliasing deoptimizations, including those via `this` pointer_

## 5.2 Keywords and Syntax Elements Ordering
Pointers and references should **always** stick to the leftmost type/qualifier

`const`/`volatile` should **always** be on the right side of the qualified type

```c++
std::uint8_t* a;
char const* b;
float&& c = 4.f;
std::uint32_t const volatile&& d = 5u;
```

_Order of keywords for a non-member variable:_
```c++
extern static thread_local constinit constexpr inline Type const volatile name;
```

_Order of keywords for a member variable:_
```c++
static thread_local constinit constexpr mutable inline Type const volatile [ms]_name;
```

_Order of keywords for a non-member function:_
```c++
extern friend static inline constexpr consteval RetType name() noexcept;
```

_Order of keywords for a member function:_
```c++
[[nodiscard]] friend virtual static constexpr consteval RetType name() const&& noexcept override final;
```

_Order of keywords for a lambda (notice the lack of spaces between the template part and parameters list):_
```c++
[this, var1, var2 = std::move(localVar2), &var3]<typename T> requires Concept<T>(Type param) static constexpr consteval mutable noexcept -> RetType {
    body;
};
```

`const`-qualified member functions have to be marked `[[nodiscard]]`

Trailing return types should be avoided in regular functions unless needed for SFINAE, but constraints and concepts with `if constexpr` checks are preferred whenever possible for this

## 5.3 Operators
For initializing POD values, `operator=` should be preferred

For initializing classes, _braced direct initialization_ should be preferred, with or without a type specification

Braced direct initialization should have a space between the data list and braces and have _no_ space on the left of the first brace

Designated initializers are welcome and should follow the order of members in a struct/class and have _no spaces_ around `=`. Values with inner whitespace should be wrapped in parenthesis (see _Example 1_)

In `return` statements no type specifier should be used. If necessary, braced initialization with/without type specification can be used

Example 1:
```c++
std::uint32_t a = 0u;
auto b = &a;

std::string c{};
std::string d{ "foo" };
foo({ .x=5.f, .y=(4.f + 3.f) });
```

Example 2:
```c++
std::optional<std::string> foo() noexcept {
    return { "foo" }; // braced initialization for conversion clarity
}
```

Logic and bitwise operators should be used in their sign form (i.e. `&&`, `!`, `^`, `|`, etc.)

When using `operator new`, parenthesis should _not_ be used if a class is later being initialized with dedicated functions (e.g. `init` in Geode mods). Otherwise, `new Class{}` is preferred for aggregates/`std::initializer_list` overloads and `new Class()` is preferred for calling constructors

**Prefix** increment/decrement should be used whenever possible (e.g. `++i` in `for` loops)

Even if the expression works with precedence, parenthesized grouping should be used in ambiguous places for clarity (e.g. `*(s->p)` instead of `*s->p`)

## 5.4 Braces
Scope braces should be placed _at the end of the line_ with a _space_

All scopes should have indentation

```c++
if (condition) {
    body1;
    body2;
}
```

```c++
void foo() noexcept {
    return;
}
```

Single scopes start and end on new lines:
```c++
statement;

{
    scoped-statement;
}

statement;
```

## 5.5 Statement Separation
Inside functions' bodies statements should be separated with newlines logically

Double/triple newlines are allowed for greater separation of more distinct pieces of logic, although it's recommended to look into moving logic into functions if this is the case

```c++
Class* create() {
    // First logic block
    auto ret = new Class;

    // Second logic block (separated)
    if (ret->init()) {
        ret->postInit();
        return ret;
    }

    // Third logic block (`return`) should normally be its own block, but "epilogue" logic can be added to it
    delete ret;
    return nullptr;
}
```

## 5.6 Control Flow
### 5.6.1 General Notes
Single-expression control flow statements can have _no braced scope_, but **only** if the scope in question is the topmost scope

Single-expression control flow statements _must_ have the expression on the following line, not on the same line

**Wrong:**
```c++
while (condition1)
    if (condition2)
        statement;
```

**Wrong:**
```c++
while (condition1) {
    if (condition2) statement;
}
```

**Correct:**
```c++
while (condition1) {
    if (condition2)
        statement;
}
```

**Wrong:**
```c++
for (std::uint8_t i = 0u; i < n; ++i) for (std::uint8_t j = 0u; j < m; ++j) {
    body;
}
```

**Correct:**
```c++
for (std::uint8_t i = 0u; i < n; ++i) {
    for (std::uint8_t j = 0u; j < m; ++j) {
        body;
    }
}
```

Try to opt for _early returns_ instead of deep nesting and branching

Keywords should have a space between the keyword and a statement/body

```c++
consteval {
    body;
}
```

```c++
if (condition) {
    body;
}
```

### 5.6.2 `do while`
`do while` blocks should have a space between the `while` keyword and a condition

```c++
do {
    body;
} while (condition);
```

`do while` statements are _preferred to use in macros if possible_ for scope encapsulation and semicolon requirement

### 5.6.3 `for`
`for` keywords should be separated from the statements block with a space

`for` loops can have a missing initializing statement _or_ a missing iteration statement. If your `for` loop is missing both, consider making it a `while` loop instead. For fully empty `for` loops, consider using `while (true)`

```c++
for (; condition; iter_statement) {
    body;
}
```

```c++
for (init_statement; condition;) {
    body;
}
```

### 5.6.4 `if`
`if` keywords should be separated from the condition with a space

Attributes `[[likely]]` and `[[unlikely]]` should be used **very sparingly** and in branches with _definitively close-to-guaranteed/never guaranteed_ chances of taking/not taking them; same goes for the `switch` statement

```c++
if (condition) [[likely]] {
    very_likely_body;
}
```

### 5.6.5 `switch`
Each `case` block should end in either a `break` or a `return` statement, unless _explicitly marked with a `[[fallthrough]]` attribute_

Each `case` block should be separated with an empty line, unless the block has a fallthrough

A `default` block _has_ to be in every `switch` statement, even if it just throws an exception

If a `case` block requires a scope, the brace begins at the `case` keyword level. Indentation level of the `break` keyword moves in that case (see below)

```c++
switch (condition) {
    case Case1: [[fallthrough]];
    case Case2:
        body1;
        [[fallthrough]];
    case Case3:
        body2;
        break;

    case Case4:
        body3;
        break;

    case Case5: {
        body4;
    } break;

    default:
        body5;
        break;
}
```

### 5.6.6 `goto`
Usage of `goto` and labels should be generally _avoided entirely_, however it is fine if this makes the code and logic _more readable_ by, for example, removing the need for condition checks to get out of deeply nested loops

```c++
while (condition1) {
    while (condition2) {
        while (condition3) {
            if (condition4)
                goto skip;
        }
    }
}
skip:
```

### 5.6.7 `try...catch`
There should be a space between the `catch` keyword and its parameters

The last `catch` in the chain should always handle every `std::exception`-derived exception

```c++
try {
    body1;
} catch (std::bad_alloc const& e) {
    body2;
} catch (std::exception const& e) {
    body3;
}
```

## 5.7 Functions
_All_ functions should be qualified as `noexcept` _whenever possible_. Exception for this rule is most Geode mods, since the entire Geometry Dash/Geode codebase doesn't allow for exceptions to be caught, nor does it follow `noexcept`-ness

A free function should either be _wrapped in a namespace_ (if external linkage is required) or _be `static`_

All functions **except constructors, destructors and `void`-returning lambdas** _must_ have a `return` statement at the end of control flow paths. _This includes non-lambda `void`-returning functions_

Functions that take no parameters should _never_ have `(void)` as the parameter list. Empty parenthesis are used instead

If `main` function's parameters aren't used they should _not_ be explicitly listed

Function _declarations_ should have _no parameter names_, unless they are a part of _public API of a library_

Explicit object parameter (aka deducing `this`) should be named `self`

### Geode Mods
In Geode mods, `create` functions should follow this pattern:

```c++
Class* create(Types... params) {
    auto ret = new Class;

    if (ret->init(params...)) {
        ret->autorelease();
        return ret;
    }

    delete ret;
    return nullptr;
}
```

Notice the absence of `CC_SAFE_DELETE`, `if (ret && ret->init(...))` and other questionable and unnecessary additions

## 5.8 Templates
The `template` keyword and the parameters statement should have a space between them

Non-concept constraining should start with a `requires` keyword at the new line, indented one level deep

Functions with multiple parameters of the same template type should have secondary parameters typed as `std::type_identity_t`, unless other behavior is expected

```c++
template <typename T>
    requires std::same_as<T, float>
void foo(T x, std::type_identity_t<T> y) {
    body;
}
```

`requires` expression should have a space between the parameter list and the body

```c++
requires (T a) {
    typename T::value;

    { a.print() };
    { a.getCount() } -> std::convertible_to<int>; // Allowed use of `int` here as a generic integer type in metaprogramming
}
```

## 5.9 Parameter Packs
Ellipsis should always stick to the _type_ if possible. This is especially relevant in declarations (`template <typename ...Args>` instead of `template <typename... Args>`). Otherwise, ellipsis stick to the _parameter_

```c++
template <typename ...Args>
void wrapper(Args&&... args) noexcept {
    foo(std::forward<Args>(args)...);

    return;
}
```

_Fold expressions_ should have a space between the ellipsis and the operation

```c++
template <typename ...Args>
    requires (
        std::derived_from<
            std::remove_cvref_t<
                std::remove_pointer_t<Args>
            >,
            CCObject
        > && ... // See `Wrapping` section for guidelines
    )
void addObjects(CCArray* arr, Args... args) {
    (arr->addObject(args), ...);

    return;
}
```

## 5.10 Lambdas
Lambdas should have _no_ space between the capture clause and the parameter list and _have_ a space between the parameter list and the body

Lambdas should _not_ have `[=]` or `[&]` as capture clauses. _All variables should be captured by value/reference or moved into capture variables **individually**_

Lambdas should _not_ have a trailing return type specified unless necessary for the return type inference

## 5.11 Macros
Macro parameters should be wrapped in parenthesis if possible

Multi-line macros should be written with one level of indentation

Multi-line macros should be wrapped in `do { ... } while (false)` blocks _whenever possible_. These blocks should _not_ have a `;` at the end

Newlines in multi-line macros should be escaped with _no_ alignment with other `\`s and with a _single space_ between the `\` and the last character of the line

```c++
#define EXAMPLE_RETURN_IF_TRUE(val, ret) \
    do { \
        if ((val)) \
            return ret; \
    } while (false)
```

## 5.12 Casts
Implicit conversions/`static_cast` should be preferred whenever possible

C-style casts (`(std::size_t)5`) are **heavily discouraged**. The only places where these are applicable are explicit value discarding (`(void) foo();`, must have a space between the cast and the function call) and casts that otherwise aren't allowed (e.g. Cocos2d-x v2's `SEL_MenuHandler`)

`reinterpret_cast` is _discouraged_, but allowed whenever necessary. Still, make sure to see if other casts (e.g. `std::bit_cast`) suit what you're doing before using it

## 5.13 Namespaces
Namespaces should have an empty line between the contents and the braces

Namespaces' contents should have _no indentation_

The closing brace should have a comment specifying a namespace in the `// namespace full::namespace::name` format

Multiple nested namespaces should be collapsed into a single one whenever possible

```c++
namespace example::utils {

void foo() noexcept;

void bar();
void baz(std::size_t, std::string_view) noexcept;

} // namespace example::utils
```


# 6. Wrapping
## 6.1 General Notes
Whenever you feel like a line of code gets too long and/or unreadable, you should try to see if wrapping it helps. There are no hard rules on line length but please don't make others know you have an ultrawide monitor via the line size

## 6.2 Functions
### 6.2.1 General Notes
Single-statement functions _could_ be wrapped onto the same line. In this case, _braces should be separated from the statement with a space_. This also allows to have `void`-returning functions with _no `return` statement_

```c++
std::views::transform([](auto&& x) { return x * 2; });
```

_Function parameters_ should be wrapped with indentation and _grouped logically_/_be on separate lines altogether_

```c++
void foo(
    Sprite const& spr, float sprScale,
    std::string_view text,
    std::size_t flag
) noexcept {
    body;
}
```

### 6.2.2 Constructors
If the initializer list does not fit on a single line it should be either:

1) Wrapped into a single, distinct line after parameters:
```c++
Class(
    float x, double y, std::string z
) : x(x), y(y), z(std::move(z)) {
    body;
}
```

Or:

2) Wrapped into an indented list:
```c++
Class(
    float x, double y, std::string z
) : x(x),
    y(y),
    z(std::move(z)) {
    body;
}
```

In both cases function parameters either shift on a separate line (like in the examples) or follow wrapping rules outlined in [6.2.1 General Notes](#621-general-notes)

## 6.3 Control Flow Elements
Conditions should be wrapped with indentation and _explicit parenthesis in case of multi-line spanning conditions_ for clarity

```c++
if (
    condition1
    ||
    !condition2
    ||
    (condition3
    &&
    condition4) || cond5 // Conditions can be wrapped onto the same line if this helps readability
) {
    body;
}
```

`for` statements should be wrapped with indentation, having a single statement per line (3 lines total)

```c++
for (
    std::uint8_t i = 0u;
    i < 34u;
    ++i
) {
    body;
}
```

## 6.4 Operators
All operators should have a space between the operator and the operands, aside from _unary operators_ (`&` as address-of, `*` as dereference, `-` as negation, `++`, etc.) and _member access operators_ (`.` and `->`)

Range pipes should be indented _**with tabs** to the first level, that starts **further than the first letter of the variable**_. Each new line should start with a pipe

```c++
auto a = arr
            | std::views::reverse 
            | std::views::take(3u);
```

Chained functions follow similar rules. Each new line should start with either `.` or `->`

```c++
auto a = Class()
            .foo()
            .bar()
            .baz();
```

Ternary operators follow similar rules. They should have the `?` at the end of the condition line, with values on the following lines and a `:` on its own line

```c++
auto a = condition ?
            trueVal
            :
            falseVal;
```

Operations on numbers should be wrapped similarly to conditional operators ([6.3 Control Flow Elements](#63-control-flow-elements))

```c++
foo(
    ((var1 + var2) // Could also be wrapped similarly to `&&` in the example
    *
    var3)
    -
    var4 + var5
);
```

## 6.5 Templates
Templates should be wrapped with one level of indentation. The next line after the last angle bracket should be _moved onto that line with a space as separation_

```c++
template <
    typename T,
    std::size_t N
> void foo(T x) noexcept {
    body;
}
```

```c++
template <
    typename T,
    std::size_t N
> requires std::integral<T>
void foo(T x) noexcept {
    body;
}
```


# 7. Final Notes
All code contributed must be _well thought of_ and _optimized_. Most of general optimization techniques and patterns aren't touched here as they have minimal correlation to codestyle, however, it's expected that contributors account for most optimization opportunities

All codestyle here is meant to serve as an _exhaustive reference_ to projects' codestyle where it applies. Anything not covered here can _still be a valid changes request/rejection reason if it's easy to infer the rules for it from surrounding code_

If you notice any inconsistencies between this document and existing (likely old) code, feel free to PR/push changes to the problematic project(s)
