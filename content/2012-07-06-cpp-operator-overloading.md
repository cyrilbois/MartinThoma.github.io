---
layout: post
title: C++ Operator overloading
slug: cpp-operator-overloading
author: Martin Thoma
date: 2012-07-06 16:00:22.000000000 +02:00
category: Code
tags: CPP
featured_image: 2012/05/CPP-thumb.png
alias: /c-operator-overloading/
---
Operator overloading is heavily used in math. One of the most famous examples I know is "+". If you add two elements from $\mathbb{N}$ you will use the same character "+" as you use for adding two numbers from $\mathbb{R}$. You even use the plus-sign if you add matrices (which is obviously something different than adding single numbers).

In some programming languages, like C++, you can overload operators by yourself.

<h2>First simple example</h2>
Imagine you wanted to store some data - lets say the prename, surname and age - about people you know. This could be done in a <code>struct</code>. After you've stored it, you would like to print this information. Obviously, you don't want to do something like this:
```cpp

for (int i=0; i< 4; i++) {
    cout << "Person(" << myArray[i].prename << " "
         << myArray[i].surname << ", " << myArray[i].age << ")";
}

```
If you wanted to print this information more than one time, you would have to add this long line every time.

A <a href="http://docs.oracle.com/javase/6/docs/api/java/lang/Object.html#toString()">toString()</a> method like the one Java uses would be nice. In C++, you don't have toString, but you can overload the <code><<</code> operator!

This is how it works:
```cpp
#include <iostream>

using namespace std;

typedef struct person {
    // attributes
    string prename;
    string surname;
    int age;

    // constructor
    person(string p, string s, int age) :
        prename(p), surname(s), age(age) {}
} Person;

// "toString" for C++
std::ostream& operator<<(std::ostream &strm, const person &a) {
  return strm << "Person(" << a.prename << " " << a.surname << ", "
              << a.age << ")";
}

int main(){
    Person Martin ("Martin", "Thoma", 22);
    Person Andreas ("Andreas", "Thoma", 22);
    Person AndiOld ("Andreas", "Berger", 30);
    Person AndiYoung ("Andreas", "Berger", 22);

    Person myArray[] = {Martin, Andreas, AndiOld, AndiYoung};

    for (int i=0; i< 4; i++) {
        cout << myArray[i] << endl;
    }

    return 0;
}
```

<h2>Sorting</h2>
You can sort by overloading <code><</code>.
You can use a sort by adding
```cpp
#include <algorithm>
```
to your program and using <code>sort(array, array + elements);</code>

This is how it looks like:
```cpp
#include <iostream>
#include <algorithm>

using namespace std;

typedef struct person {
    // attributes
    string prename;
    string surname;
    int age;

    // constructor
    person(string p, string s, int age) :
        prename(p), surname(s), age(age) {}
} Person;

// ".equals()" for C++
bool operator<(const Person& a, const Person& b){
    if (!(a.prename == b.prename)) {
        return a.prename < b.prename;
    } else if (!(a.surname < b.surname)) {
        return a.surname < b.surname;
    } else {
        return a.age < b.age;
    }
}

// "toString" for C++
std::ostream& operator<<(std::ostream &strm, const person &a) {
  return strm << "Person(" << a.prename << " " << a.surname << ", "
              << a.age << ")";
}

int main(){
    Person Martin ("Martin", "Thoma", 22);
    Person Andreas ("Andreas", "Thoma", 22);
    Person AndiOld ("Andreas", "Berger", 30);
    Person AndiYoung ("Andreas", "Berger", 22);

    Person myArray[] = {Martin, Andreas, AndiOld, AndiYoung};

    sort(myArray, myArray + 4);

    for (int i=0; i< 4; i++) {
        cout << myArray[i] << endl;
    }

    return 0;
}
```

By the way, if you don't define <code><</code> you get something like this:
```cpp
In file included from /usr/include/c++/4.4/algorithm:62,
                 from operators.cpp:2:
/usr/include/c++/4.4/bits/stl_algo.h: In function &lsquo;const _Tp& std::__median(const _Tp&, const _Tp&, const _Tp&) [with _Tp = person]&rsquo;:
/usr/include/c++/4.4/bits/stl_algo.h:2268:   instantiated from &lsquo;void std::__introsort_loop(_RandomAccessIterator, _RandomAccessIterator, _Size) [with _RandomAccessIterator = Person*, _Size = int]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:5220:   instantiated from &lsquo;void std::sort(_RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
operators.cpp:34:   instantiated from here
/usr/include/c++/4.4/bits/stl_algo.h:89: error: no match for &lsquo;operator<&rsquo; in &lsquo;__a < __b&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:90: error: no match for &lsquo;operator<&rsquo; in &lsquo;__b < __c&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:92: error: no match for &lsquo;operator<&rsquo; in &lsquo;__a < __c&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:96: error: no match for &lsquo;operator<&rsquo; in &lsquo;__a < __c&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:98: error: no match for &lsquo;operator<&rsquo; in &lsquo;__b < __c&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h: In function &lsquo;_RandomAccessIterator std::__unguarded_partition(_RandomAccessIterator, _RandomAccessIterator, _Tp) [with _RandomAccessIterator = Person*, _Tp = person]&rsquo;:
/usr/include/c++/4.4/bits/stl_algo.h:2268:   instantiated from &lsquo;void std::__introsort_loop(_RandomAccessIterator, _RandomAccessIterator, _Size) [with _RandomAccessIterator = Person*, _Size = int]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:5220:   instantiated from &lsquo;void std::sort(_RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
operators.cpp:34:   instantiated from here
/usr/include/c++/4.4/bits/stl_algo.h:2209: error: no match for &lsquo;operator<&rsquo; in &lsquo;* __first < __pivot&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:2212: error: no match for &lsquo;operator<&rsquo; in &lsquo;__pivot < * __last&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h: In function &lsquo;void std::__insertion_sort(_RandomAccessIterator, _RandomAccessIterator) [with _RandomAccessIterator = Person*]&rsquo;:
/usr/include/c++/4.4/bits/stl_algo.h:2178:   instantiated from &lsquo;void std::__final_insertion_sort(_RandomAccessIterator, _RandomAccessIterator) [with _RandomAccessIterator = Person*]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:5222:   instantiated from &lsquo;void std::sort(_RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
operators.cpp:34:   instantiated from here
/usr/include/c++/4.4/bits/stl_algo.h:2106: error: no match for &lsquo;operator<&rsquo; in &lsquo;__val < * __first&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h: In function &lsquo;void std::__heap_select(_RandomAccessIterator, _RandomAccessIterator, _RandomAccessIterator) [with _RandomAccessIterator = Person*]&rsquo;:
/usr/include/c++/4.4/bits/stl_algo.h:5067:   instantiated from &lsquo;void std::partial_sort(_RAIter, _RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:2256:   instantiated from &lsquo;void std::__introsort_loop(_RandomAccessIterator, _RandomAccessIterator, _Size) [with _RandomAccessIterator = Person*, _Size = int]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:5220:   instantiated from &lsquo;void std::sort(_RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
operators.cpp:34:   instantiated from here
/usr/include/c++/4.4/bits/stl_algo.h:1906: error: no match for &lsquo;operator<&rsquo; in &lsquo;* __i < * __first&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h: In function &lsquo;void std::__unguarded_linear_insert(_RandomAccessIterator, _Tp) [with _RandomAccessIterator = Person*, _Tp = person]&rsquo;:
/usr/include/c++/4.4/bits/stl_algo.h:2112:   instantiated from &lsquo;void std::__insertion_sort(_RandomAccessIterator, _RandomAccessIterator) [with _RandomAccessIterator = Person*]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:2178:   instantiated from &lsquo;void std::__final_insertion_sort(_RandomAccessIterator, _RandomAccessIterator) [with _RandomAccessIterator = Person*]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:5222:   instantiated from &lsquo;void std::sort(_RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
operators.cpp:34:   instantiated from here
/usr/include/c++/4.4/bits/stl_algo.h:2067: error: no match for &lsquo;operator<&rsquo; in &lsquo;__val < * __next&rsquo;
In file included from /usr/include/c++/4.4/bits/stl_algo.h:62,
                 from /usr/include/c++/4.4/algorithm:62,
                 from operators.cpp:2:
/usr/include/c++/4.4/bits/stl_heap.h: In function &lsquo;void std::__adjust_heap(_RandomAccessIterator, _Distance, _Distance, _Tp) [with _RandomAccessIterator = Person*, _Distance = int, _Tp = person]&rsquo;:
/usr/include/c++/4.4/bits/stl_heap.h:394:   instantiated from &lsquo;void std::make_heap(_RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:1904:   instantiated from &lsquo;void std::__heap_select(_RandomAccessIterator, _RandomAccessIterator, _RandomAccessIterator) [with _RandomAccessIterator = Person*]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:5067:   instantiated from &lsquo;void std::partial_sort(_RAIter, _RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:2256:   instantiated from &lsquo;void std::__introsort_loop(_RandomAccessIterator, _RandomAccessIterator, _Size) [with _RandomAccessIterator = Person*, _Size = int]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:5220:   instantiated from &lsquo;void std::sort(_RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
operators.cpp:34:   instantiated from here
/usr/include/c++/4.4/bits/stl_heap.h:232: error: no match for &lsquo;operator<&rsquo; in &lsquo;*(__first + ((unsigned int)(((unsigned int)__secondChild) * 12u))) < *(__first + ((((unsigned int)__secondChild) + 0xffffffffffffffffffffffffffffffffu) * 12u))&rsquo;
/usr/include/c++/4.4/bits/stl_heap.h: In function &lsquo;void std::__push_heap(_RandomAccessIterator, _Distance, _Distance, _Tp) [with _RandomAccessIterator = Person*, _Distance = int, _Tp = person]&rsquo;:
/usr/include/c++/4.4/bits/stl_heap.h:244:   instantiated from &lsquo;void std::__adjust_heap(_RandomAccessIterator, _Distance, _Distance, _Tp) [with _RandomAccessIterator = Person*, _Distance = int, _Tp = person]&rsquo;
/usr/include/c++/4.4/bits/stl_heap.h:394:   instantiated from &lsquo;void std::make_heap(_RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:1904:   instantiated from &lsquo;void std::__heap_select(_RandomAccessIterator, _RandomAccessIterator, _RandomAccessIterator) [with _RandomAccessIterator = Person*]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:5067:   instantiated from &lsquo;void std::partial_sort(_RAIter, _RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:2256:   instantiated from &lsquo;void std::__introsort_loop(_RandomAccessIterator, _RandomAccessIterator, _Size) [with _RandomAccessIterator = Person*, _Size = int]&rsquo;
/usr/include/c++/4.4/bits/stl_algo.h:5220:   instantiated from &lsquo;void std::sort(_RAIter, _RAIter) [with _RAIter = Person*]&rsquo;
operators.cpp:34:   instantiated from here
/usr/include/c++/4.4/bits/stl_heap.h:134: error: no match for &lsquo;operator<&rsquo; in &lsquo;*(__first + ((unsigned int)(((unsigned int)__parent) * 12u))) < __value&rsquo;
```

<h2>Equality</h2>
You can also define <code>==</code> for your structs.

I know this example does NOT make any sense. But it is an example you can work with:
```cpp
#include <iostream>

using namespace std;

typedef struct person {
    // attributes
    string prename;
    string surname;
    int age;

    // constructor
    person(string p, string s, int age) :
        prename(p), surname(s), age(age) {}
} Person;

// "comperator" for C++
bool operator==(const Person& a, const Person& b){
    return a.age == 30;
}

int main(){
    Person Martin ("Martin", "Thoma", 22);
    Person Andreas ("Andreas", "Thoma", 22);
    Person AndiOld ("Andreas", "Berger", 30);
    Person AndiYoung ("Andreas", "Berger", 22);

    Person myArray[] = {Martin, Andreas, AndiOld, AndiYoung};

    for (int i=0; i< 4; i++) {
        cout << (myArray[i] == myArray[i]) << endl;
    }

    return 0;
}
```

<h2>Casting</h2>
You can also define casts:
```cpp
#include <iostream>

using namespace std;

typedef struct person {
    // attributes
    string prename;
    string surname;
    int age;

    // constructor
    person(string p, string s, int age) :
        prename(p), surname(s), age(age) {}

    // prefix
    operator int() { return age; }
} Person;

int main(){
    Person Martin ("Martin", "Thoma", 22);
    Person Andreas ("Andreas", "Thoma", 22);
    Person AndiOld ("Andreas", "Berger", 30);
    Person AndiYoung ("Andreas", "Berger", 22);

    Person myArray[] = {Martin, Andreas, AndiOld, AndiYoung};

    for (int i=0; i< 4; i++) {
        cout << int(myArray[i]) << endl;
    }

    return 0;
}
```

<h2>Adding new operators</h2>
I like Python very much. Python allows me to get the power of a number like this:
```python
a = 2 ** 10  # 1024
```

Lets try it for C++:
<h3>Doesn't work</h3>
```cpp
#include <iostream>

using namespace std;

// does NOT work
// operators.cpp:7: error: expected initializer before &lsquo;*&rsquo; token
int operator**(int a, int b){
    int power = 1;
    for (int i=0; i < b; i++) {
        power *= a;
    }
    return power;
}

int main(){
    cout << 2**10 << endl;

    return 0;
}
```
I guess it doesn't work as it would be very difficult to distinguish something like this:
```cpp
a = a * *b;
a = a ** b;
```

If you try to use a <code>$</code> you get:
```cpp
operators.cpp:16:13: error: invalid suffix "$10" on integer constant
```

If you try to use a <code>&sect;</code> you get:
```cpp
operators.cpp:7: error: stray &lsquo;\302&rsquo; in program
operators.cpp:7: error: stray &lsquo;\247&rsquo; in program
operators.cpp:16: error: stray &lsquo;\302&rsquo; in program
operators.cpp:16: error: stray &lsquo;\247&rsquo; in program
operators.cpp:7: error: expected type-specifier before &lsquo;(&rsquo; token
```

You are also not allowed to redefine <code>*</code>:
```cpp
operators.cpp:7: error: &lsquo;int operator*(int, int)&rsquo; must have an argument of class or enumerated type
```

<h3>Works</h3>
You can wrap the integer like this:
```cpp
#include <iostream>

using namespace std;

typedef struct integer {
    int inner;

    // constructor
    integer(int i) : inner(i) {}
} Integer;

int operator^(Integer a, Integer b){
    int power = 1;
    for (int i=0; i < b.inner; i++) {
        power *= a.inner;
    }
    return power;
}

int main(){
    cout << (Integer(2)^Integer(10)) << endl; // outputs 1024
    return 0;
}
```

<h2>See also</h2>
<ul>
  <li>A class for <a href="../fractions-in-cpp/">dealing with fractions</a> - which includes 7 examples for operator overloading</li>
  <li><a href="http://en.wikipedia.org/wiki/Operators_in_C_and_C%2B%2B">Operators in C and C++</a></li>
  <li><a href="http://stackoverflow.com/a/4421715/562769">The General Syntax of operator overloading in C++</a>. sbi, Stack Overflow.</li>
  <li><a href="http://stackoverflow.com/a/4421708/562769">The Three Basic Rules of Operator Overloading in C++</a>. sbi, Stack Overflow.</li>
  <li><a href="http://www.cplusplus.com/doc/tutorial/classes2/">Overloading operators</a>. C++-Reference.</li>
</ul>
