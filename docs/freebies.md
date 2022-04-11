# Freebies
Here are things that you get for free if you wish to work in Python.

- [AKDSFramework](https://github.com/theroyakash/AKDSFramework), a Purely written in Python library containing implementations of various data structures. See documentations [here $\to$](https://docs.akdsframework.theroyakash.com/).
- Benchmarking decorator: Use an one line import to profile any python function, `from AKDSFramework.applications.decorators import benchmark`. More on this here in this [blog post $\to$](https://publications.theroyakash.com/benchmark-your-python-program).
- A Python based caching decorator, to use in Dynamic Programming problems. More on this here in this [blog post $\to$](https://publications.theroyakash.com/cache-your-code).
- A big O analyser, find it [here $\to$](https://publications.theroyakash.com/introducing-an-efficient-big-o-analyzer). It works with most of the python programs but is not a good fit for more complex functions.
- [TinyDS](https://github.com/theroyakash/tinyds), small and simple pythonic implementations for most of your coding problems that are asked in SDE I or II interviews at FAANGs that you will be able to implement on the spot if asked.

## C++
- I actually now prefer to solve algorithm problems in C++ now, so AKDSFramework alternative is here for C++. [Click to get it on Github $\to$](https://github.com/theroyakash/tinydscpp).
- How to use the `stdin` and `stdout` and take input from files and write output to text files to quickly submit solutions to online coding platforms. [Here is an article with that information](/stdinout-usage).

### Writing C++ Output with color
Distinguishing C++ output inside the terminal is a hard job. Use the following color pallette to distinguish the outputs.

```cpp
#define RESET "\033[0m"
#define BLACK "\033[30m"              /* Black */
#define RED "\033[31m"                /* Red */
#define GREEN "\033[32m"              /* Green */
#define YELLOW "\033[33m"             /* Yellow */
#define BLUE "\033[34m"               /* Blue */
#define MAGENTA "\033[35m"            /* Magenta */
#define CYAN "\033[36m"               /* Cyan */
#define WHITE "\033[37m"              /* White */
#define BOLDBLACK "\033[1m\033[30m"   /* Bold Black */
#define BOLDRED "\033[1m\033[31m"     /* Bold Red */
#define BOLDGREEN "\033[1m\033[32m"   /* Bold Green */
#define BOLDYELLOW "\033[1m\033[33m"  /* Bold Yellow */
#define BOLDBLUE "\033[1m\033[34m"    /* Bold Blue */
#define BOLDMAGENTA "\033[1m\033[35m" /* Bold Magenta */
#define BOLDCYAN "\033[1m\033[36m"    /* Bold Cyan */
#define BOLDWHITE "\033[1m\033[37m"   /* Bold White */
```

### Usage with `std::cout`
Everytime you use any of the color you must reset the color from the terminal like the following:

```cpp
int main() {
    vector<int> v = {1, 0, 1, 0, 0};

    for (auto i : v)
        cout << BOLDCYAN << i << " " << RESET;

    cout << " \n";
    return 0;
}
```
## Algorithms C++
C++ Algorithms is a simple data structure implementation library that you can remember to implement in an interview setting.

## Contributing
There is some open positions for contribution in **AKDSFramework** **tinyds**, and **C++ Algorithms** projects. If you are willing to work open source please reach out to me at [hey@theroyakash.com](mailto:hey@theroyakash.com).

!!! warning Important
    Note that I am currently a masters student in computer science, so I won't be able to provide financial reimbursement for any of the contributions done for the project. If that does not bother you, please reach out.
