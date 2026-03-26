<!-- omit from toc -->
# C++ Contribution Code Standards
This document explains standards that the C++ code you contribute must adhere to. Enforcement of specific points is up to the reviewer.

<!-- omit from toc -->
# Table of Contents
- [1. Environment Configuration](#1-environment-configuration)
  - [1.1 Files](#11-files)
  - [1.2 Tooling](#12-tooling)
  - [1.3 Project Structure](#13-project-structure)
    - [Geode Mods](#geode-mods)
- [2. Naming Conventions](#2-naming-conventions)
  - [2.1 Variables](#21-variables)
  - [2.2 Misc Entities](#22-misc-entities)
  - [2.3 Class Members](#23-class-members)
  - [2.4 Files](#24-files)
  - [2.5 CMake](#25-cmake)
  - [Geode Mods](#geode-mods-1)
  - [Notes](#notes)
- [3. File Structure and Whitespace](#3-file-structure-and-whitespace)
  - [3.1 Header File Structure](#31-header-file-structure)
  - [3.2 Source File Structure](#32-source-file-structure)
  - [Notes](#notes-1)
- [4. Structs/Classes/Unions](#4-structsclassesunions)
  - [4.1 General Information](#41-general-information)
  - [4.2 Structure](#42-structure)
    - [4.2.1 Structs](#421-structs)
    - [4.2.2 Classes](#422-classes)
    - [4.2.3 Unions](#423-unions)
  - [4.3 Notes](#43-notes)
- [5. Keywords and Syntax Elements Ordering](#5-keywords-and-syntax-elements-ordering)


# 1. Environment Configuration
## 1.1 Files
All indentation should be in _tabs_ (`\t`, ASCII `0x9`). This can be semi-automatically handled by most text editors/IDEs

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
## 2.1 Variables
Local variables - `camelCase`

Static to _source file_ variables - `s_camelCase`

Static to _function_ variables - `camelCase`

Global variables are **discouraged entirely**

Template variables - `camelCase_v`

Variable names should be _descriptive_. Names like `i` and `j` are permitted as generic iteration variables. `c` could be used as a `char` variable for string iteration

## 2.2 Misc Entities
Macros - `PROJECT_NAME_UPPER_SNAKE_CASE`

_All_ functions - `camelCase`

Structs/Classes/Unions - `PascalCase`

Enums/Class enums - `PascalCase`

Enum members - `PascalCase` **(discouraged unless scoped externally)**

Class enum members - `PascalCase`, `UPPER_SNAKE_CASE` if "internal" (e.g. a `COUNT` member)

Namespaces - `snake_case`, but generally names should be kept **relatively short** and at **one word**

Concepts - `PascalCase`

Modules - `snake_case`

`using` aliases - `PascalCase` _with no `_t` suffix_, **aside from template aliases similar to `std::type_identity_t`**

`typedef` aliases are **discouraged entirely**

Template parameters:
- `T` for a single, generic (possibly constrained) type
  - Semantic, _short_ names in `PascalCase` for more than one type
- `N` for integer NTTPs
  - Semantic, _short_ names in `PascalCase` OR fitting letters (e.g. `A` for `std::meta::info`) for other NTTPs

## 2.3 Class Members
Member variables - `m_camelCase`

Static variables - `s_camelCase`

## 2.4 Files
Class files - according to a class name (e.g. a file containing the definition of class `HelperClass` should be named `HelperClass.cpp`)

General files - `kebab-case.ext`

## 2.5 CMake
Variables - `UPPER_SNAKE_CASE`

Modules and extra files - `kebab-case.cmake` in `cmake/`

## Geode Mods
Hooked classes - `HClassName` (e.g. `HMenuLayer` as a hooked `MenuLayer`)

## Notes
`const(expr)`-ness does _not_ affect naming

Short forms like `str` for string, `spr` for sprite, `btn` for button are permitted in local variable names but _not in function names_


# 3. File Structure and Whitespace
## 3.1 Header File Structure
- `#pragma once`
- _Empty line_
- Project headers (included with `""`, added globally via `target_include_directories` in CMakeLists.txt)
- _Empty line_
- Framework's headers
- _Empty line_
- 3rd party headers
- _Empty line_
- Standard library headers
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

## 3.2 Source File Structure
- Header for implementation _[optional if the source file implements the header]_
- _Empty line [optional]_
- Project headers (included with `""`, added globally via `target_include_directories` in CMakeLists.txt)
- _Empty line_
- Framework's headers
- _Empty line_
- 3rd party headers
- _Empty line_
- Standard library headers
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


void Helper1::foo() {
    // ...
}

void Helper1::bar() {
    // ...
}


void Helper2::baz() {
    // ...
}
```

## Notes
Within their categories headers should preferably be ordered _by their usage in the code_ (e.g. if you're using `<string_view>` a few lines before you use `<span>`, `#include <string>` should come first)

More detailed whitespace and indentation rules regarding specifically functions, namespaces, classes etc. are described further in their respective sections


# 4. Structs/Classes/Unions
## 4.1 General Information
Structs should be used for simple general purpose aggregates (even if the structure in question isn't an aggregate by definition). May have custom constructors but _no custom/RAII destructor_. Members follow `camelCase`

Classes should be used for Object-Oriented Programming. This means classes can have member functions, non-`public` members, nested classes, etc. Members follow dedicated conventions, described in [2.3 Class Members](#23-class-members)

Unions should rarely be used due to type-safe `std::variant` existing. Otherwise follow rules similar to structs. **Union-based type punning is heavily discouraged due to being UB with better alternatives!**

_All_ of these types must be marked `final` if applicable

## 4.2 Structure
### 4.2.1 Structs
Should have raw members, optionally with a custom constructor(s). Constructor(s) should be put on top of members with one line of separation and use member initializer lists. Inline initializers are allowed

Example:
```c++
struct Example1 final {
    Example1(std::uint64_t* pointer, std::int16_t number) : pointer(pointer), number(number) {
        std::println("Constructed with {} and {}", pointer, number);
    }

    std::string string{};
    std::uint64_t* pointer;
    std::int16_t number;
};
```

### 4.2.2 Classes
Can have anything OOP related.

The order for access sections is as follows:
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

Access specifiers indentation should be at the `class` keyword level. Access sections must be separated with a newline. Specific access sections may be occasionally interrupted with other access sections for better readability (see `init` in the example). Beginning of member variable sections must be marked with a `// Fields` comment. Inline initializers are allowed and preferred for static members over definition in a TU. Static functions should appear before non-static ones in their respective access sections. Static members should appear before other members and be separated with a newline

Copy/move constructors/assignment operators must be deleted if unneeded and mentioned in the following order (along with constructors and destructors):
- Basic constructor(s)
- Copy constructor
- Copy assignment operator
- Move constructor
- Move assignment operator
- Destructor

If some of these functions are being mass deleted they may have no newline between them

Example:
```c++
class Example2 final : public Base {
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
Rules for unions similar to structs, see [4.2.1 Structs](#421-structs)

## 4.3 Notes
**Both structs and classes MUST have their layout optimized for type alignment!** As a first step try reordering members in descending order of their sizes, then account for potential stricter alignment specified with `alignas`. If unsure check the object layout for holes with tools like `pahole`

Consider using `alignas` to optimize cache locality if the type is close enough to applicable sizes and is vastly used in arrays


# 5. Keywords and Syntax Elements Ordering
TODO