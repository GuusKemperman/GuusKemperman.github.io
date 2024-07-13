---
layout: post
title: "How exceptions make your game faster"
date: 2024-07-13
tags: Coral
---

Exceptions are a powerful feature in C++, yet rarely used in game programming. Their usage is even advised against in game development courses and studies, with a commonly cited reason being performance. 

A pattern that is now more commonly used is returning error codes, ```std::optional``` or ```std::expected```. This blog post explores why exceptions are a more efficient choice for handling errors when performance is a priority, and how I've used exceptions to increase the performance of Coral Engine.

## Bad rep

On some older consoles, C++ exceptions either were not supported at all, or came with a huge recommendation to disable them as they would be very detrimental to performance [[1]](#sources). In the past, books stated that exception handling was slow for C++ and not the right tool for game development [[2]](#sources).

When compiling for x86, there is a cost even when exceptions are not thrown. The compiler generates additional code to keep track of the current "scope" which is later used to determine what destructors to call and where to start searching for exception filters and handlers. Scope changes are triggered by try blocks and the creation of objects with destructors [[3]](#sources). 

In modern days, with modern consoles and with everyone running on x64 [[4]](#sources), these issues have lost their relevance.

## Benefits

Exceptions inform the compiler that some paths only get executed in exceptional circumstances, opening up the door for optimizations that cannot be achieved by alternative methods of error handling.

**Cold Code Placement**

Your CPU loads instructions into their cache and registers. It's quite unfortunate if you fill this tiny storage up with instructions for code that never get executed. Exception handling code is moved to a less frequently executed part of the codebase, the 'cold' part, leaving more room for the instructions of the hot code paths. 

**Improved Branch Prediction**

CPUs try to work a bit in advance, they'll gamble that a certain branch will be taken; if they guess right, great! If not, they rollback the last few instructions and proceed along the actual path. The compiler *may* optimize branch predictions knowing that exceptions are rare events, similar to C++20's [```[[likely]]```](https://en.cppreference.com/w/cpp/language/attributes/likely). This allows the main code path to run faster.

**Reduced Branching**

You no longer have to check return values every step along the way. This not only improves readability by reducing boilerplate error-checking code but also reduces the number of branches, enhancing performance. Instead of having multiple checks like ```if (result.has_value())``` after every function call, an exception can handle the error once, simplifying the main code flow.

**Simplifying Returned Types**

Exceptions are aimed towards zero-overhead on the successful path, but ```std::optional``` and ```std::expected``` do not share this benefit. They have to check if they hold a value every time you try to access them. They are also slightly larger, for example with ```std::expected``` you pay the cost of storing the error type, even when no error occurred!

## Results

To see the practical impact of using exceptions, I applied this approach to the visual script interpreter in Coral Engine. There were several dozen places where error states were returned and checked, in functions that were called thousands of times per second. I switched to throwing exceptions instead and having a single try/catch block.

Performance Improvement: **5.6%**

Not only are exceptions not slowing us down; they are even faster than traditional error checking. Additionally, the code has become cleaner as we no longer need to check return values at every step.

## What's the catch?

Exceptions are faster, but can also opens your API up to misuse; It is not clear to a user that a function can throw an exception without relying on them reading the documentation.

```cpp
std::filesystem::path GetUserDataFile();
Image LoadTexture(const std::filesystem::path& pathToPng);
std::string ReadFile(const std::filesystem::path& binaryFile);
```

A user could easily forget to use a try-catch block. I personally believe that code should be self-documenting; APIs should be difficult to misuse, and users should explicitly choose how to handle error. By not making the user aware of the possibility of failure, the program could crash in an instance where it could have safely been handled. I prefer returning an error type than relying on the user to remember to catch the exception.

While we've focused on the improved performance of the successful path, throwing exceptions can be quite slow. Therefore, exceptions should be used exclusively to signal exceptional circumstances. Optimizations should aim to minimize the occurrence of these exceptions, ensuring that the normal execution path remains efficient.

## Conclusion

Design your APIs so that the user is aware of possible errors, but don't shy away from using exceptions for the sake of performance; they have their place in performance-critical codebases. Embrace exceptions where appropriate, and you'll find your code both cleaner and faster.

### Sources

- [[1][2]](https://www.gamedev.net/forums/topic/689321-performance-with-using-exceptions-in-c-game-programming/5345647/) *Exceptions being historically slow (anecdotal)*
- [[3]](https://stackoverflow.com/questions/3744984/performance-when-exceptions-are-not-thrown-c#:~:text=x86%2C%20there%20is%20a%20cost%20even%20when%20exceptions%20are%20not%20thrown.%20The%20compiler%20generates%20additional%20code%20to%20keep%20track%20of%20the%20current%20%22scope%22%20which%20is%20later%20used%20to%20determine%20what%20destructors%20to%20call%20and%20where%20to%20start%20searching%20for%20exception%20filters%20and%20handlers) *x86 Overhead*
- [[4]](https://store.steampowered.com/hwsurvey/Steam-Hardware-Software-Survey-Welcome-to-Steam) *x64 Usage*
- [[5]](https://github.com/GuusKemperman/CoralEngine/commit/6387c403b3e8b670ace17f6917f72c71b269c467) *Link to commit where I switched to using exceptions in the virtual machine (repository may not be public yet at the time of reading). Benchmark ran for 5 minutes. Four benchmarks were ran in total, consistently 4-6% faster.* 
   * *With ```Expected```: AvgDt (ms): 164.7, Standard deviation (%) : 1.7*
   * *With exceptions: AvgDt (ms): 156,4 Standard deviation (%) : 2.1*
