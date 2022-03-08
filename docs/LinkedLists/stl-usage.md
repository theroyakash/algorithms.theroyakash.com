---
title: Linked List STL operations
tags:
    - `STL`
---

# Linked List STL operations
Instead of implementing linked list from scratch, better to use the standard template library. C++ `stl` has 2 linked lists,

- [`std::list<typename>` is a doubly-linked list data structure](https://en.cppreference.com/w/cpp/container/list), and
- [`std::forward_list<typename>` is a singly-linked list data structure](https://en.cppreference.com/w/cpp/container/forward_list).

## `std::list<typename>`
- supports constant time insertion and removal of elements from anywhere in the container
- Adding, removing and moving the elements within the list or across several lists does not invalidate the iterators or references. An iterator is invalidated only when the corresponding element is deleted.

### Operations
- `front()` and `back()` returns front and back elements from the list in $O(1)$ time.

```cpp
#include <list>
#include <iostream>
 
int main() {
    std::list<char> letters {'d', 'm', 'g', 'w', 't', 'f'};
 
    if (!letters.empty()) {
        std::cout << "The first character is '" << letters.front() << "'.\n";
        std::cout << "The last character is '" << letters.back() << "'.\n";        
    }
}
```
- `size()` returns size of the list in constant time since C++ 11.

```cpp
#include <list>
#include <iostream>
 
int main() { 
    std::list<int> nums {1, 3, 5, 7};
 
    std::cout << "nums contains " << nums.size() << " elements.\n";
}
```

#### Operation Modefiers
Adding elements, all done in $O(1)$ time.
- `push_back()`
- `pop_back()`
- `pop_back()`
- `pop_front()`

#### Essential operations
**`merge()`** function merges 2 sorted linked lists.
```cpp
#include <iostream>
#include <list>
 
// For help in printing lists
std::ostream& operator<<(std::ostream& ostr, const std::list<int>& list) {
    for (const auto &i : list) {
        ostr << ' ' << i;
    }
    return ostr;
}


int main() {
    
    std::list<int> list1 = { 5,9,1,3,3 };
    std::list<int> list2 = { 8,7,2,3,4,4 };
 
    list1.sort();
    list2.sort();
    std::cout << "list1:  " << list1 << '\n';
    std::cout << "list2:  " << list2 << '\n';
    list1.merge(list2);
    std::cout << "merged: " << list1 << '\n';
    
    return 0;
}
```
## `std::forward_list<typename>`
- `front()` and `end()` are available.
- `end()` returns the iterator to the next element of the end of the list in constant time.
- `front()` is also $O(1)$ and is equivalent to `*list.begin()`
- **No `size()`** available. But has `max_size()` method, returns the maximum number of elements the container is able to hold due to system or library implementation limitations.
- `numbers.empty()` method checks if the container has no elements, i.e. whether `begin() == end()`.