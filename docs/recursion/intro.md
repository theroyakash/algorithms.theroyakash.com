# Introduction to recursion
Recursion is when a [function] calls itself until some condition is met. Following things we need to understand very clearly before proceeding with recursion

## What is this **making problem space smaller** idea?

We are not specifically looking for to reduce the problem space, instead we'll be making some decisions that will reduce the problem space. Reduction of problem space is a by-product of the decisions that we'll be making in a specific stage of the algorithm.

For recursive function calls a new stack space is created and the function will have no memory of what were the variables in the previous function call. To avoid this we use parameterized recursive calls.

## Easier Recursion problems
### Print some useful information 10 times
Following is the code for printing "something useful" 10 times with recursion,

#### Approach
- Static and global variables are stored in heap which is shared across all function calls. So we'll store a counter inside the heap to keep track of the counter across all function calls.

```cpp
void print(){
    static int count = 10;
    if (count != 0){
        cout << "Something\n";
        count--;
        print();
    } else {
        return;
    }
}

int main() {
    print();
    return 0;
}
```

### Similarly print 1 to 10 using recursive calls

```cpp
void print(){
    static int count = 10;
    if (count != 0){
        cout << count <<"\n";
        count--;
        print();
    } else {
        return;
    }
}

int main() {
    print();
    return 0;
}
```