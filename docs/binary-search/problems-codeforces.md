# Binary Search Codeforces Problems

This section includes problems from Codeforces (Mostly Div. 2). All the problems are selected from Striver's CP Sheet.

**Problems**

- [Hamburgers](#hamburgers)
- [Magic Powder 1 \& 2](#magic-powder-1--2)


## [Hamburgers](https://codeforces.com/contest/371/problem/C)
### Problem Statement (Revised)
Find the original problem statement in the codeforces website.

- To make hamburgers we need a bread, sausage and cheese. We write down the recipe of "Le Hamburger de Polycarpus" as a string of letters 'B' (bread), 'S' (sausage) and 'C' (cheese).
- The ingredients in the recipe go from bottom to top, for example, recipe "ВSCBS" represents the hamburger where the ingredients go from bottom to top as bread, sausage, cheese, bread and sausage again.
- We have $n_b, n_s, n_c$ many bread-sausage-cheese in the kitchen.
- We also have access to bread-sausage-cheese in a nearby shop with price tag $p_b, p_s, p_c$.
- We have total of ₹$r$. With $n_b, n_s, n_c$ many bread-sausage-cheese and ₹$r$ how many Hamburger we can make using a given recipe.

### Approach
- We do a binary search on the `numberOfBurgers`. We stop when we find the highest `numberOfBurgers` feasable to build. This pattern is called binary search on answer.

### Code
```cpp
#include <iostream>
#include <string.h>
#include <vector>

using namespace std;

bool isFeasableWithNumberOfBurgers(long long int numberOfBurgers,
                                  long long int recipeRatio[],
                                  long long int kitchen[],
                                  long long int price[],
                                  long long int rupees) {

    // bread cost is either the extra Bread Needed or 0 if enough in the kitchen
    long long int breadCost = price[0] * max(numberOfBurgers * recipeRatio[0] - kitchen[0], (long long int)0);
    long long int sausageCost = price[1] * max(numberOfBurgers * recipeRatio[1] - kitchen[1], (long long int)0);
    long long int cheeseCost = price[2] * max(numberOfBurgers * recipeRatio[2] - kitchen[2], (long long int)0);

    long long int totalcost = breadCost + sausageCost + cheeseCost;

    if (rupees < totalcost) {
        return false;
    }

    return true;
}

int main() {
    
    string recipe;
    cin >> recipe;

    long long int recipeRatio[3] = {0, 0, 0};

    for (char ingredient : recipe) {
        if (ingredient == 'B') recipeRatio[0]++;
        if (ingredient == 'S') recipeRatio[1]++;
        if (ingredient == 'C') recipeRatio[2]++;
    }

    long long int kitchen[3];
    cin >> kitchen[0] >> kitchen[1] >> kitchen[2];

    long long int price[3];
    cin >> price[0] >> price[1] >> price[2];

    long long int rupees;
    cin >> rupees;


    // binary search on answer
    long long int maxBurgerPossible = rupees + 1000;

    long long int start = 0;
    long long int end = maxBurgerPossible;

    while (start <= end) {
        long long int mid = start + (end - start) / 2;
        bool possible = isFeasableWithNumberOfBurgers(mid, recipeRatio, kitchen, price, rupees);
        if (possible) {
            start = mid + 1;
        } else {
            end = mid - 1;
        }
    }

    cout << end << endl;

    return 0;
}
```

## Magic Powder 1 & 2
Find the problem on [codeforces](https://codeforces.com/contest/670/problem/D1)
### Problem Statement
Solving problem 670/problem/D2 also solves 670/problem/D1 as D1 is with smaller constraints.

- We have $n$ ingredients that are needed in the following ratio $a[i] \: \forall i \in [n]$. $a[i]$ grams of $i^{\text{th}}$ ingredient is needed to make the cookie.
- Our kitchen has $b[i] \: \forall i \in [n]$ grams of the $i^{\text{th}}$ element.
- We also have access to $k$ grams of magic powder that can be used for any element that we are short of.
- Find the maximum cookie can be made.

### Approach
Similar to the last problem we find what is the highest amount of cookie that can be made via a binary search on the number of cookies via this `checkIfPossibleToMakeNumberOfCookies()` function.


### Code
```cpp
#include <iostream>
#include <string.h>
#include <vector>

using namespace std;

bool checkIfPossibleToMakeNumberOfCookies(unsigned long long int numberOfCookies,
                                   unsigned long long int numberOfIngredients,
                                   unsigned long long int demand[],
                                   unsigned long long int kitchen[],
                                   unsigned long long int magicPowder) {
    unsigned long long int totalDeficiency = 0;
    for (unsigned long long int i = 0; i < numberOfIngredients; i++) {
        if (numberOfCookies * demand[i] > kitchen[i]) {
            totalDeficiency += numberOfCookies * demand[i] - kitchen[i];
        }
    }

    if (totalDeficiency > magicPowder) return false;

    return true;
}

int main() {
    unsigned long long int ingredients, magicPowder;
    cin >> ingredients >> magicPowder;

    unsigned long long int demand[ingredients];  // demand
    unsigned long long int kitchen[ingredients]; // kitchen

    for (unsigned long long int i = 0; i < ingredients; i++) {
        cin >> demand[i];
    }

    for (unsigned long long int i = 0; i < ingredients; i++) {
        cin >> kitchen[i];
    }

    unsigned long long int start = 1;
    unsigned long long int end = 1e12;

    while (start <= end) {
        unsigned long long int mid = start + (end - start) / 2;
        bool possible = checkIfPossibleToMakeNumberOfCookies(
            mid, ingredients,
            demand, kitchen, magicPowder
        );

        if (possible) {
            start = mid + 1;
        } else {
            end = mid - 1;
        }
    }

    cout << end << endl;

    return 0;
}
```