# CSES Editorial on Searching and Sorting

This is the CSES Editorial Section on Searching and Sorting problems. Entire Problem list can be found [here](https://cses.fi/problemset/list/).

## Editorials

??? Success "Distinct Numbers"
    [Problem Link](https://cses.fi/problemset/task/1621).

    Use set to store the distinct numbers. Testcases are designed such a way that the usage of `unordered_set` creates a $O(n^2)$ blow-up. Read [here](https://codeforces.com/blog/entry/62393).
    
    ??? danger "Code"
        ```cpp
        int main () {
            int n;
            cin >> n;
        
            set<int> s;
        
            for (int i = 0; i < n; i++) {
                int x;
                cin >> x;
        
                s.insert(x);
            }
        
            cout << s.size() << endl;
        
            return 0;
        }
        ```

??? Success "Apartments"
    After sorting both the array, use a two pointer approach, do `j++` `while (j < m and b[j] < a[i] - k)`, then the first element is the apartment matched with `a[i]` if `b[j] <= a[i] + k` still holds true. If that's the case do `j++` to select the next apartment and do `i++` for the next candidate.

    ??? danger "code"
        ```cpp
        void solve() {
            int n, m, k;
            cin >> n >> m >> k;
        
            int a[n];
            for (int i = 0; i < n; i++) {
                cin >> a[i];
            }
        
            int b[m];
            for (int i = 0; i < m; i++) {
                cin >> b[i];
            }
        
            sort(a, a + n);
            sort(b, b + m);
        
            int assigned = 0;
        
            for (int i = 0, j = 0; i < n; i++) {
                while (j < m and b[j] < a[i] - k) {
                    j++;
                }
        
                if (j < m and b[j] <= a[i] + k) {
                    assigned++; j++;
                }
            }
            
            cout << assigned << endl;
        }
        ```

??? success "Ferris Wheel"
    We need to maximize the number of pairs, then the answer would be $n -$ number of pairs many boats needed. Each boat has $x$ as the total capacity. We start two pointers at $i = 0$ and $j = n - 1$. While we have `while (i < j and (p[i] + p[j] > x)) j--;` then if `i < j` then we can choose two pairs, we do `pairs++` and `j--, i++`. At the end we do `cout << n - pairs << endl;`.

    ??? danger "Code"
        ```cpp
        void solve() {
            int n, x;
            cin >> n >> x;
            int p[n];
        
            for (int i = 0; i < n; i++) {
                cin >> p[i];
            }
        
            sort(p, p + n);
        
            int pairs = 0;
        
            for (int i = 0, j = n - 1; i < j;) {
                // maximize the number of pairs
                while (i < j and (p[i] + p[j] > x)) j--;
                if (i >= j) {
                    break;
                }
        
                pairs++; j--; i++;
            }
        
            cout << n - pairs << endl;
        }
        ```

??? success "Concert Tickets"
    We need multiset to store the ticket values. As soon as some query comes, we need to give output to the `lower_bound` from the set and then remove it from the set. For the `lower_bound` to work properly we need a `multiset<int, greater<int>> h;`.

    ??? danger "Code"
        ```cpp
        void solve() {
            int n, m;
            cin >> n >> m;
        
            multiset<int, greater<int>> h;
        
            int t[m];
        
            for (int i = 0; i < n; i++) {
                int x; cin >> x; h.insert(x);
            }
        
            for (int i = 0; i < m; i++) {
                cin >> t[i];
            }
        
            for (int j = 0; j < m; j++) {
                auto it = h.lower_bound(t[j]);
        
                if (it != h.end()) {
                    cout << *it << endl;
                    h.erase(it);
                } else {
                    cout << -1 << endl;
                }
            }
        }
        ```

??? success "Restaurant Customers"
    Add each of the starting and ending time in some list. Then do a `+1` for incoming public and a `-1` for the outgoing public. This will store the maximum public present at a given time $t$.

    ??? danger "Code"
        ```cpp
        void solve() {
            int n;
            cin >> n;

            vector<pair<int, int>> time_count;

            for (int i = 0; i < n; i++) {
                pair<int, int> joining;
                cin >> joining.first; joining.second = 1;

                pair<int, int> leaving;
                cin >> leaving.first; leaving.second = -1;

                time_count.push_back(joining);
                time_count.push_back(leaving);
            }

            std::sort(time_count.begin(), time_count.end(), [](const auto a, const auto b) {
                return a.first < b.first;
            });

            int max_count = INT_MIN;
            int total_cnt = 0;

            for (auto pairs:time_count) {
                total_cnt += pairs.second;
                max_count = std::max(total_cnt, max_count);
            }

            cout << max_count << endl;
        }
        ```

??? success "Movie Festival"
    Simple greedy strategy is enough to solve this. Same problem as interval scheduling on classic algorithms text-books. Sort based on the ending time, then select the earliest finishing movie, and reject conflicting timing movies.

    First we count how many removals we need (removals of conflicting movies). In our approch we sort them via their ending time, then we remove the longer event if there is a conflict. We update the removal counter, at the end $n - \text{removals}$ is our solution.
    
    ??? danger "Code"
        ```cpp
        void solve() {
        
            // classic interval scheduling problem,
            // sort based on the ending time and choose the maximum
        
            unsigned long long int n;
            cin >> n;
        
            pair<unsigned long long int, unsigned long long int> a[n];
        
            for (int i = 0; i < n; i++) {
                cin >> a[i].first >> a[i].second;
            }
        
            std::sort(a, a + n, [](const auto a, const auto b) {
                return a.second < b.second;
            });
        
            int removals = 0;
        
            int i = 0;
            while (i < n - 1) {
                if (a[i].second > a[i + 1].first) {
                    // this is where there is a conflict
                    a[i + 1] = a[i];
                    removals++;
                }
        
                i++;
            }
        
            std::cout << n - removals << std::endl;
        }
        ```

??? success "Sum of Two Values"
    Basic two sum - equivalent problem. Create a `map` and store the `<a[i], i>` inside if you don't find `x - a[i]` inside map. Otherwise return `i, map[x - a[i]]` the first time you find it.

    ??? danger "Code"
        ```cpp
        void solve() {
            int n, x;
            cin >> n >> x;

            int a[n];
            map<int, int> mp;

            for (int i = 0; i < n; i++) {
                cin >> a[i];
                if (mp.find(x - a[i]) == mp.end()) {
                    mp.insert({a[i], i});
                } else {
                    cout << i + 1 << " " << mp[x - a[i]] + 1 << endl;
                    return;
                }
            }

            cout << "IMPOSSIBLE" << endl;
        }
        ```

??? success "Maximum Subarray Sum"
    Kedane's algorithm is to be followed for a $O(n)$ submission. There will be some edge cases to sort through.

    ??? danger "Code"
        ```cpp
        void solve() {
            long long int n; cin >> n;
        
            long long int sum = 0;
            long long int max_sum = -1;
        
            long long int max_elem = LLONG_MIN;
        
            for (long long int i = 0; i < n; i++) {
                long long int x; cin >> x;
                max_elem = std::max(max_elem, x);
                
                sum += x;
        
                if (sum < 0) sum = 0; 
                max_sum = std::max(sum, max_sum);
            }
        
            long long int ans = max_sum ? (max_sum) : max_elem;
            cout << ans << endl;
        }
        ```

??? success "Stick Lengths"
    It is an well known problem, we need to change everything to median. Then we find out the cost.

    ??? danger "Code"
        ```cpp
        void solve() {
            long long int n; cin >> n;
            long long int p[n];
            
        
            for (int i = 0; i < n; i++) {
                cin >> p[i];
            }
        
            sort(p, p + n);
        
            long long int cost = 0;
            long long int median = p[n / 2];
        
            for (long long int i = n - 1; i >= 0; i--) {
                cost += abs(median - p[i]);
            }
        
            cout << cost << endl;
        }
        ```

??? success "Missing Coin Sum"
    Maintain a variable called `looking_for` and set it to $1$, to start with. As the array is sorted if the first element is $> 1$ then there is no chance we find $1$, hence $1$ is the smallest answer.

    Then we move on to second element, say the second element is $x \neq 2$, then $2$ must be the smallest answer. Suppose $2$ is present at the second location, then we don't need to look for $3$, hence we update `looking_for += 2 (a[i])`. Suppose the next element is again $2$ then we update the `looking_for` by $2 (a[i])$, hence now we'll be `looking_for` $5$.

    Suppose the next element is $x_{4}$. Suppose $x_4 <$ `looking_for`. That means we don't find this element in the array. So that should be the minimum answer.

    ??? danger "Code"
        ```cpp
        void solve() {
            unsigned long long int n; cin >> n;

            unsigned long long int a[n];

            for (int i = 0; i < n; i++) {
                cin >> a[i];
            }

            sort(a, a + n);

            unsigned long long int looking_for = 1;
            for (int i = 0; i < n; i++) {
                if (looking_for < a[i]) {
                    cout << looking_for << endl;
                    return;
                }

                looking_for += a[i];
            }

            cout << looking_for << endl;
        }
        ```

??? success "Collecting Numbers I"
    We first store all the element's index as a reverse lookup table. Then we iterate over a variable `currently_looking`. If `currently_looking - 1` is present left of `currently_looking` then we can include in the current run, then we go to `currently_looking + 1`,

    If we find some `currently_looking - 1` is present right of `currently_looking` that means we hit a break. We resume operation from `currently_looking + 1` after updating the correct collection count `collections`.

    ??? danger "Code"
        ```cpp
        void solve() {
            int n;
            cin >> n;
            long long int a[n];

            map<long long int, int> mp;

            for (int i = 0; i < n; i++) {
                cin >> a[i]; mp.insert({a[i], i});
            }

            int collections = 1;
            int currently_looking = 2;

            while (currently_looking <= n) {
                if (mp[currently_looking] < mp[currently_looking - 1]) {
                    collections++;
                }

                currently_looking++;
            }

            cout << collections << endl;
        }
        ```

??? success "Playlist"
    Use a `map<a[i], count(a[i])>` for subarray $a[i \dots j]$ such that all $a[k] \in (i, j)$ is unique. Leaving each `a[i]`, decrease the count from the `map`.

    ??? danger "Solution"
        ```cpp
        void solve() {
            int n;
            cin >> n;

            int a[n];

            map<int, int> mp;

            for (int i = 0; i < n; i++) {
                cin >> a[i];
            }

            int mxcnt = -1;
            for (int i = 0, j = 0; i < n; i++) {
                while (j < n and mp[a[j]] < 1) {
                    mp[a[j]]++;
                    j++;
                }

                mxcnt = std::max(mxcnt, j - i);
                mp[a[i]]--;
            }

            std::cout << mxcnt << std::endl;
        }
        ```