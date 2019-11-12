# New bracket initializer rules.

#### Key difference (in short):
`auto x {1};`

C++17: 
> x is an int.

C++11/14: 
> x is std::initializer_list\<int\> with only one element.

C++17 sets x to be an int.

C++11/14 sets x to be std::initializer_list\<int\>, which is seldom what we would want. Now, with C++17, we still can set a variable to be of type std::initializer_list<int>, but we need to do that in more explicit way. To do that, we need to use '=' before '{' (it's called a copy-list-initialization) for initializing a single-element std::initializer_list<int> to a variable.

`auto x2 {1, 2};`

C++17: 
> error: direct-list-initialization of 'auto' requires exactly one element.
> note: for deduction to 'std::initializer_list', use copy-list-initialization (i.e. add '=' before the '{')


C++11/14:
> x2 is std::initializer_list<int>

##### Summary
C++17 explicitly forbids us to directly initialize an auto variable with anything else than a single element. It also sets the initialized variable to be exactly of type of that value it's initialized with.


#### What the bracket initialization gives us again?

* Explicit initialization with a constuctor call.
* Prevention from implicit type conversions—exact matching with the constructor. (while initialization with parenthaseses try to match the closest constructor and do implicit (you may not know about them) type conversions.
* Prevention from reinitialization.

Let's clarify how to correctly initialize variables in C++17.

## What are direct-list-initialization & copy-list-initialization
direct-list-initialization: `auto x4 {1};`

copy-list-initialization: `auto x4 = {1};` (i.e. add '=' before the '{')


### Initializations without auto type deduction
```C++
int x1 = 1;
int x2 {1};
int x3 (1);

std::vector<int> v1 {1, 2, 3}; // vector with 3 ints: 1, 2, 3
std::vector<int> v2 = {1, 2, 3}; // same here
std::vector<int> v3 (10, 20); // vector with 20 ints: each with value of 20
```

### Initializations without auto type deduction
```C++
auto x4 = 1;
auto x5 {1};
auto x6 {1, 2, 3}; // ERROR, only single element in direct auto initialization allowed! (this is new)

auto i4 = {1};    // i4 is std::initializer_list<int>
auto i5 = {1, 2}; // i5 is std::initializer_list<int>
auto i6 = {1, 2, 3.0};  //ERROR: failed to deduce type
