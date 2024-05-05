Top-down dynamic programming has always come naturally to me. As long as I can find a way to bruteforce the problem, then I can just throw in some memoization and boom you got dynamic programming.

That's not the best way to think of problems though, and bottom-up DP is nearly always more efficient, usually memory-wise.

In these notes I'm going to go over the most common DP problems and explain how to find the bottom-up solution, hopefully a few examples should be enough to ingrain in my brain how it works.

# [Coin Change](https://leetcode.com/problems/coin-change/)

### Top Down Solution

As I said, finding the top-down solution always starts from finding the recursive bruteforce solution first, and then caching method calls.

In this problem, we're asking ourselves what's the least amount of coins we have to use to reach this amount? Well, we can have a decision tree where the number of branches is the number of coins we have at our disposal.

Let's take the example coins = \[1, 2, 5] and amount = 11.
We're going to start asking ourselves: "assuming we take the first coin (1), how many other coins do we need to reach 11?" That will be our first branch. The second branch will be "assuming we take the second coin (2), how many other coins do we need to reach 11?", likewise, we'll do the same thing for 5.
The rest of the three will be the same thing, until we reach 11 or go over it.
To memoize and improve the time complexity (DP) we'll use a dictionary to cache function calls. This way we're mapping the amount we need to the minimum number of coins to reach that value.

Thinking of what it means to memoize the top-down solution can bring us closer to logically explain the bottom-up solution too. We want to store, for each "amount" (from 0 to the amount), what the minimum number of coins is to reach that amount.

### Bottom Up Solution

Bottom up means we're starting from the bottom (basically an amount of 0) and then use these computed sub-problems to find the answer to the bigger problem.

Let's use the previous example `coins = [1, 2, 5], amount = 11`

- how many coins to have an amount of 0? well, 0
- how many coins to have an amount of 1? let's iterate over all coins and check:
  - with the current coin I have, let's find the answer to the precomputed solution, what's the least number of coins I need to reach the amount of (1 - currentCoin)?
  - well in this case it will be the coin 1, and the others will go over. so let's store this result somewhere (an array is easier)
- how many coins to have an amount of 2? let's iterate over all coins:
  - with the 1st coin (1): we can use this one and then we would need an amount of 1 to reach 2 (1+1). what's the least number of coins we need to reach 1? Well, luckily we've already computed that problem and stored it in an array dp.
    - Therefore, `dp[2] = 1 + dp[1]` -> 2
  - with the 2nd coin (2): we can use this one and then we'd need an amount of 2 to reach 2 (0+2), therefore: `dp[2] = 1 + d[0]` -> 1
  - with the 3rd coin (5): we can use this one and then we'd need an amount of -3 to reach 2 (-3+5). We're not checking negative amounts obviously so we can skip over this coin.
  - That's it! We've found out that to reach an amount of 2, the minimum number of coins we need is 1.
- we keep doing this until we reach the amount we want. as you can see, we're using precomputed solutions to reach the bigger solution.

```cpp
class Solution {

public:
    int coinChange(vector<int>& coins, int amount) {
        int dp[amount + 1];
        dp[0] = 0;

        for(int i = 1; i < amount + 1; i++){
            dp[i] = INT_MAX; // default value
            for(const auto& c: coins){
                if(i - c >= 0 && dp[i - c] != INT_MAX){
                    dp[i] = min(dp[i], 1 + dp[i - c]);
                }
            }
        }
        return dp[amount] != INT_MAX ? dp[amount] : -1;
    }
};
```
