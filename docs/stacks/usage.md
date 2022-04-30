---
title: Stack Usage
description: Most of the Stack Problems asked in interviews [for SDE I and II].
---

# :material-stack-overflow: Implementation of Stacks and Queues
A stack and Queue provides 2 basic operations, for stack it is `push()` and `pop()` and for queues it is `enqueue()`, `dequeue()`. Using Python's collection's deque we can implement both stack as well as queue in no time.

In an interview setting you can implement stacks and queues from scratch in about 30-40 lines of code, for that I'll recommend you to use the following implementations.

- [Stacks from Scratch](https://github.com/theroyakash/tinyds/blob/main/tinyds/stacks.py)
- [Queue from Scratch](https://github.com/theroyakash/tinyds/blob/main/tinyds/queues.py)

In interview setting I highly recommend you to use the `collections.dequeue` to implement the stack and queue classes.

## Source Code

```python
from collections import deque
from rich.console import Console

console = Console()  # For printing purposes

class QueueWithDequeue:
    def __init__(self, iterable=None):
        if not iterable:
            self.queue = deque()
        else:
            self.queue = deque(iterable)
    
    def dequeue(self):
        # Automatically will raise Error if the queue is empty
        return self.queue.popleft()
    
    def enqueue(self, value):
        self.queue.append(value)
        
    def show(self):
        console.print([data for data in self.queue])
```

## Stacks implementation with Deque Module
```python
class Stack:
    def __init__(self, iterable=None):
        if not iterable:
            self.container = deque()
        else:
            self.container = deque(iterable)
        
    def pop(self):
        return self.container.pop()
    
    def push(self, val):
        return self.container.append(val)
    
    def tos(self):
        # Get the top of the stack
        return self.container[-1]
    
    def __repr__(self):
        return f"{[data for data in self.container]}"
```

## Stacks implementation with C++
With C++ STL stack implementation is really easy with vector. Here is a simple example of implementation of MinStack that stores elemenents as well as return min value in $O(1)$ time.
```cpp
#include <iostream>
#include <vector>

using namespace std;

class MinStack{
private:
    vector<int> stack;
    vector<int> auxStack;
    int ClassPrivateSize;

public:
    MinStack(){
        ClassPrivateSize = 0;
    }

    int size(){
        return ClassPrivateSize;
    }

    void push(int value){

        if (ClassPrivateSize == 0){
            stack.push_back(value);
            auxStack.push_back(value);
            ClassPrivateSize += 1;
        }else{
            if (value < auxStack.back()){
                stack.push_back(value);
                auxStack.push_back(value);
            }else{
                stack.push_back(value);
            }
            ClassPrivateSize += 1;
        }
    }

    int pop(){
        if (ClassPrivateSize == 0){
            throw runtime_error("pop(): Empty Stack");
        }else{

            if (auxStack.back() == stack.back()){
                int value = stack.back();
                auxStack.pop_back();
                stack.pop_back();

                return value;
            }else{
                int value = stack.back();
                stack.pop_back();
                return value;
            }
            ClassPrivateSize -=1;
        }
    }

    bool isEmpty() const{
        if (ClassPrivateSize == 0){
            return true;
        }
        return false;
    }

    int min(){
        return auxStack.back();
    }

    int peek() {
        return stack.back();
    }

};
```