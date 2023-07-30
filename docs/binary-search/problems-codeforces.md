# Binary Search Codeforces Problems

This section includes problems from Codeforces (Mostly Div. 2). All the problems are selected from Striver's CP Sheet.

**Problems**

- [Hamburgers](#hamburgers)
- [Magic Powder 1 \& 2](#magic-powder-1--2)
- [Poisoned Dagger](#poisoned-dagger)
- [Pipeline](#pipeline)


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

## Poisoned Dagger
### Problem Statement
- In a game there is a monster. Game is for $100^{500}$ second long. In this game, his character has to kill a dragon. 
- Player attacks the dragon with a poisoned dagger. The $i^{\text{th}}$ attack is performed at the beginning of the $a[i]$ second from the battle start.
-  The dagger itself does not deal damage, but it applies a poison effect on the dragon, which deals $1$ damage during each of the next $k$ seconds (starting with the same second when the dragon was stabbed by the dagger). However, if the dragon has already been poisoned, then the dagger updates the poison effect (i.e. cancels the current poison effect and applies a new one).


For example, suppose $k = 4$, and Monocarp stabs the dragon during the seconds $2, 4$ and $10$. Then the poison effect is applied at the start of the 2-nd second and deals 1 damage during the $2^{\text{nd}}$ and $3^{\text{rd}}$ seconds; then, at the beginning of the $4^{\text{th}}$ second, the poison effect is reapplied, so it deals exactly $1$ damage during the seconds $4, 5, 6$ and $7$; then, during the $10^{\text{th}}$ second, the poison effect is applied again, and it deals 1 damage during the seconds 10, 11, 12 and 13. In total, the dragon receives 10 damage.

Find the minimum possible value of $k$ (the number of seconds the poison effect lasts) that is enough to deal at least $h$ damage to the dragon.

### Approach
- Standard Binary Search on Answer.
- To check feasability we write `findFeasability` method. In there we do a merge interval subroutine for the attack window, then count the total damage done during the entire time. 

### Code

```cpp
#include <iostream>
#include <string.h>
#include <vector>

using namespace std;

bool findFeasability(unsigned long long int k,
                     unsigned long long int n,
                     unsigned long long int intervals[],
                     unsigned long long int h) {
    // merge intervals
    vector<vector<unsigned long long int>> startEndIntervals(n);

    for (int i = 0; i < n; i++) {
        startEndIntervals[i] = {intervals[i], intervals[i] + k - 1};
    }

    vector<vector<unsigned long long int>> mergedIntervals;

    int i = 0;
    unsigned long long int last = startEndIntervals.size() - 1;

    while (i < last) {
        if (startEndIntervals[i][1] >= startEndIntervals[i + 1][0]) {
            // means there is an overlap
            startEndIntervals[i + 1][0] = startEndIntervals[i][0];                                        // minimum starting time
            startEndIntervals[i + 1][1] = std::max(startEndIntervals[i][1], startEndIntervals[i + 1][1]); // maximum
        } else {
            mergedIntervals.push_back(startEndIntervals[i]);
        }

        i++;
    }

    mergedIntervals.push_back(startEndIntervals[i]); // add the last interval

    // count total damage from the mergedIntervals list
    unsigned long long int totalDamage = 0;
    for (auto intervalPair : mergedIntervals) {
        totalDamage += intervalPair[1] - intervalPair[0] + 1;
    }

    return totalDamage >= h;
}

int main() {
    int testcases;
    cin >> testcases;

    while (testcases--) {
        unsigned long long int n, h;
        cin >> n >> h;

        unsigned long long int a[n];

        for (int i = 0; i < n; i++) {
            cin >> a[i];
        }

        unsigned long long int start = 1;
        unsigned long long int end = 1e19;

        while (start <= end) {
            unsigned long long int mid = start + (end - start) / 2;
            bool feasable = findFeasability(mid, n, a, h);
            if (feasable) {
                end = mid - 1;
            } else {
                start = mid + 1;
            }
        }

        cout << start << endl;
    }
    return 0;
}
```

## Pipeline

### Problem Statement
- We have $n$ houses.
- We have $2 \dots k$ many $1 \to i$ way splitters.
- We need to find minimum $j \in \{2 \dots k\}$ such that we have $\geq n$ many pipeline output.

### Approach
- We need to do a binary search on answer.
- For feasability condition we do the following
    - Use $k-1 \to j$ many splitters for $j$ many spitters.
    - Total output of such usage of splitters is $\binom{k}{2} - \binom{j}{2}$.
    - Find if $\binom{k}{2} - \binom{j}{2} \geq n$.


### Code
```cpp
#include <iostream>

using namespace std;

int main () {
    unsigned long long int n, k;
    cin >> n >> k;

    if (n == 1) {
        cout << 0 << endl;
        return 0;
    }

    if (n <= k) {
        cout << 1 << endl;
        return 0;
    }

    unsigned long long int start = 2;
    unsigned long long int end = k + 2;

    unsigned long long int maximumSplittedPipes = k*(k + 1) / 2 - k;
    long long int minimumSplitters = -1;

    while (start <= end) {
        unsigned long long int mid = start + (end - start) / 2;
        // use from mid -> k many splitters

        unsigned long long int currentTotalSplit = maximumSplittedPipes - ((mid * (mid - 1) / 2) - mid);

        if (currentTotalSplit >= n) {
            minimumSplitters = k - mid + 1;
            start = mid + 1;
        } else {
            end = mid - 1;
        }
    }

    cout << minimumSplitters << endl;

    return 0;
}
```