---
layout: post
title: "How exceptions make your game faster"
date: 2024-07-15
tags: Coral
---

# The days before std::function - C-style function pointers

```cpp
int free_add(int a, int b) { return a + b; }

struct Foo
{
	static int member_add(int a, int b) { return a + b; }
};

auto lambda_add = [](int a, int b) -> int { return a + b; };

struct
{
	int operator()(int a, int b) const {
		return a + b;
	};
} functor_add;

int main()
{
	int(*invokePtr) (int, int) = &free_add; // 1
	invokePtr = &Foo::member_add; // 2
	invokePtr = lambda_add; // 3
	invokePtr = functor_add; // 4
	invokePtr = [&](int a, int b) { return functor_add(a, b); }; // 5
}
```

Which lines compile? 1, 2, & 3.

But did you know function references were a thing?

```cpp
int(&invokeRef1) (int, int) = free_add; // 1
int(&invokeRef2) (int, int) = Foo::member_add; // 2
int(&invokeRef3) (int, int) = lambda_add; // 3
int(&invokeRef4) (int, int) = functor_add; // 4
int(&invokeRef5) (int, int) = [&](int a, int b) { return functor_add(a, b); }; // 5
```

Now 3 no longer compiles :(

Then std::function came to save us all. All of them are valid with std::function!

```cpp
std::function<int(int, int)> invokeFunc = &free_add; // 1
invokeFunc = &Foo::member_add; // 2
invokeFunc = lambda_add; // 3
invokeFunc = functor_add; // 4
invokeFunc = [&](int a, int b) { return functor_add(a, b); }; // 5
```

Side-note: if you're feeling old school, std::function still has 

# How does std::function work? How do we make our own?

# Problems with std::function - And how does C++26 solve them?