# Hashing problems
If some problem is not solvable without mental gymnastics, you can pretty much solve those with a hashtable or `unordered_map<d_t, d_t>` in C++.

Let's solve some of the hashing problems which may not be straight forward.

## Find and Replace Pattern
### Problem Statement
Given a list of strings words and a string pattern, return a list of `words[i]` that match pattern. You may return the answer in any order.

A word matches the pattern if there exists a permutation of letters p so that after replacing every letter x in the pattern with p(x), we get the desired word.

Recall that a permutation of letters is a bijection from letters to letters: every letter maps to another letter, and no two letters map to the same letter.

### Approach
- We can solve this problem with a hash table but the problem is one letter is mapped to only one letter. Let's take some examples see how this can cause some problems.
- Let's say we have a pattern `abb` and we want to match from `["abc","deq","mee","aqq","dkd","ccc"]`. Now if we make an unordered map with `abb` to for each string mapping it will show us that `mee` is matching, `aqq` is matching and also `ccc` is matching. You can only check if `a` is mapped to only `c` or not, not the other way around. So the test case `acc` will fail our algorithm. So let's try to map the reverse way round.
- We'll map the string from the string array to the pattern string. Now the test case `mee` will have `m->a` mapping, `e->b` mapping and once more `e->b` mapping. Now let's take a look at the test case `abc`, `a->a` mapping then `b->c` mapping and then `c->b` mapping. So there is another error now. Once b is mapped to b then c can not be mapped to b. b can be only mapped to b.
- So we've looked at both directions and none of the seems to work, what if we have a bi directional `char` to `char` mapping so that we can match one letter to exactly one letter?
- So we need to implement a `BiMap()` class to solve the problem.

Below is the implementation for **BiMap**.

```cpp
class BiMap {
private:
    unordered_map<char, char> front;
    unordered_map<char, char> back;

public:
    BiMap() {}
    
    void put(char key, char value) {
        front.insert({key, value});
        back.insert({value, key});
    }
    
    char checkFront(char key) {
        if (front.find(key) == front.end()) return '~';
        return front[key];
    }
    
    char checkBack(char value) {
        if (back.find(value) == back.end()) return '~';
        return back[value];
    }
};
```

Now using that **BiMap** we'll solve the problem.

```cpp
class Solution {
private:
    bool findMatching(string word, string pattern) {
        
        if (word.size() != pattern.size()) return false;
        
        int index = 0;
        
        BiMap b = BiMap();
        
        while (index < pattern.size()) {
            char charFromWord = word[index];
            char charFromPattern = pattern[index];
            
            // if bimap returns ~ means it is not in the bimap
            // if both front and back returns ~ that means this is a new character
            if (b.checkFront(charFromWord) == '~' and b.checkBack(charFromPattern) == '~')
                b.put(charFromWord, charFromPattern);
            
            // else if checkFront and checkBack should return identical mapping
            // m->a and a->m
            // check if it is the same with the pattern?
            if (b.checkFront(charFromWord) != charFromPattern) {
                return false;
            }
            
            if (b.checkBack(charFromPattern) != charFromWord) {
                return false;
            }
            
            index++;
        }
        
        return true;
    }

public:
    vector<string> findAndReplacePattern(vector<string>& words, string pattern) {
        vector<string> v;
        for (string str:words) {
            if (findMatching(str, pattern)) {
                v.push_back(str);
            }
        }
        
        return v;
    }
};
```