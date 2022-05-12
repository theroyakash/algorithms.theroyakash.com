# Two heaps problems
These are the problems that generally requires two binary heaps i.e. a min heap and a max heap. We can divide the problem in two parts, we'll use a Min Heap to find the smallest element and a Max Heap to find the biggest element and combine the parts together.

Let's see some questions on this.

## Find the Median of a Number Stream
Design a class to calculate the median of a number stream. The class should have the following two methods

- `insertNum(int num)`: stores the number in the class
- `findMedian()`: returns the median of all numbers inserted in the class

Just like a normal median function, if the count of numbers inserted in the class is even, the median will be the average of the middle two numbers.

### Example
1. `insertNum(3)`
2. `insertNum(1)`
3. `findMedian()` $\to$ output: 2
4. `insertNum(5)`
5. `findMedian()` $\to$ output: 3
6. `insertNum(4)`
7. `findMedian()` $\to$ output: 3.5