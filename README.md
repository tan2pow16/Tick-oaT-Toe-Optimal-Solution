# Tick-oaT-Two Optimal Solution

Strategy for P2 derived by `Tangent65536`.

A friend of mine told me about a small game they came across on YouTube. Turns out it's really fun and there is an optimal solution when playing as the 2<sup>nd</sup> player (P2)! Maybe you should try to figure that out on your own first, and see if your solution works! `^.^`  

---

## Origin
Tick-oaT-Two is a simple [game](https://www.tickoattwo.com/) made by [Oats Jenkins](https://www.youtube.com/@OatsJenkins).  
Check out his [fun video](https://www.youtube.com/watch?v=ePxrVU4M9uA) too!

---

## Rules
1. Similar to a 3x3 tic-tac-toe, a player wins by completing a line. However,  
2. The player who first finished a "fully completed" line wins. That means the line must be filled by both players.  
3. One may not tailgate -- each player cannot simply repeat the exact moves of their opponent right afterward.  
   This removes the advantage for P2. Or does it?

Or just try the game yourself!

---

## Hints
### Spoiler alert! It reduces the fun! Try the game first!
Here's some crucial information you may want to identify:
1. How many slots could you fill in without forming a line (including a diagonal)? What's the maximum one may achieve?
2. Following 1., what happens when you fill in just 1 more slot?

Meanwhile, don't forget that the goal is to complete a line P1 has already finished!

---

## Observations
### Spoiler alert! Try the game before reading this!
Here are the answers to the questions above.  
1. You may fill in at most 6 slots without forming any line. (Keep in mind that it has both rotational and mirror symmetry)
```
-------------
| + | + |   |
-------------
| + |   | + |    Note that once you claim a corner, the pattern is determined.
-------------
|   | + | + |
-------------
```
2. If there's a given line you want to complete, you can always do it with just 1 step by filling in the corresponding slot, except the "void diagonal".
3. With 7 slots filled, there will always be at least 2 complete lines. You can easily prove that by filling the whole board first and removing any 2 slots.

---

## Solution
### Spoiler alert! It ruins the fun!

To simplify the notation, I'll use a (big-endian-alike) 3-bit value to represent the state of a slot.
```
 [4] [2] [1]
-------------
| 1 | 0 | 1 |
-------------
  |   |   |- P1 claim bit
  |   |
  |   |- P2 claim bit
  |
  |- Tailgate lock bit (cannot be claimed in the next move)
```
The example above (value 5) means the slot containing such value has just been claimed by P1 and it's now P2's turn.  

The key point lies in that at a certain point, P1 will have to fill in 7 slots before P2. As P2 can decide which slot to fill in _after_ P1, the optimal solution for P2 is to follow the pattern above while leaving at least 1 slot filled in by P1 in the "void diagonal". Following this rule, one may derive the first set of strategies playing as P2:

1. **Do NOT touch the center before completing the mentioned pattern.**
2. **Do NOT claim any corner before P1.**

Intuitively, one may say it's optimal to claim a neighboring corner when P1 first claimed one. Indeed, this leaves at least 1 slot in P2's "void diagonal" claimed and unlocked by P1. However, there's a catch with this strategy:
```
-------------
| 2 | 3 | 1 |
-------------
| 3 | 0 | 3 |    P1's turn! 1/3 chance for P1 to get it right! ^.^
-------------
| 1 | 7 | 2 |
-------------
```
In the example above, P1 can claim the center slot and lock P2 out of finishing any line P1 has finished, as all 3 of them go through the center locked by P1. In such case, P2 always loses.

How about claiming the diagonal corner immediately after P1 has first claimed a corner? Let's see what happens.

In the 1st scenario, P1 claimed a neighboring corner or the center before P2 finish the pattern. One may prove that P1 will always complete a line with just 6 slots claimed. This means at the 7th move for P2, there is always an unlocked slot claimed by P1 that P2 can produce a line P1 has already finished. Voila! P2 wins!
```
-------------
| 2 | 3 | 0 |
-------------
| 3 | 0 | 3 |    P1's turn! P2 has already won! Can you see why? ^.^
-------------
| 1 | 7 | 3 |
-------------
```  
  
In the 2nd scenario, Both players produce the exact same pattern at their 6th move.
```
-------------
| 3 | 3 | 0 |
-------------
| 3 | 0 | 3 |    P1's turn! Now what?
-------------
| 0 | 7 | 3 |
-------------
```
As one may easily see, the next move from P2 will lock out P1 from finishing any line completed by P2 at the moment, as the crucial intersection will always land on the "void diagonal" and be locked from tailgating. However, P1's 7th move, which is the crucial intersection for P2 to win the game, will be unlocked after P1's 8th move.
```
-------------
| 3 | 3 | 0 |
-------------
| 3 | 6 | 3 |    P1's turn! P2 has already won!
-------------
| 1 | 3 | 3 |
-------------
```
Therefore, this strategy is feasible for P2 and forms rule no. 3.

3. **When P1 claims their first corner, claim the corner at the diagonally opposite position immediately. Now your target pattern (or the "void diagonal") is fixed.**

Nevertheless, there's a minor catch -- as P2, you cannot just randomly fill in a target slot according to the given pattern. In a specific case, P2 may run into the scenario that P1 locked the last slot required to finish the required pattern, forcing P2 to complete a line.

Example:
```
-------------
| 0 | 3 | 3 |
-------------    P2's turn!
| 3 | 0 | 3 |    However, the bottom-left corner cannot be claimed by P2 now.
-------------    P1 has already won! Oof!
| 5 | 3 | 0 |
-------------
```

Note that when the required pattern is complete, since `(6 + 6) % 9 == 3`, there will always be at least 3 co-claimed slots. As a result, P2 can always avoid getting locked out by "preserving" a slot in the required pattern claimed 2 steps ahead by P1. Always choose one to preserve unless it's obvious that P1 cannot lock you out. That's rule no. 4.  

4. **If the required pattern is determined, claim the empty slots in the pattern first.**

If you're keen on minor details, you may already have a question: _what if P1 forces P2 to claim a corner first by filling in the center slot?_  
Good question! Remember that it only takes 6 slots to finish a line given that the center is filled in? Thus, when P1 claims the center slot, P2 can freely choose one of the 4 corners no matter what P1 does in later moves.

5. **If P1 forces P2 to claim a corner first, it is up to P2 to decide which corner to fill in.**

This set of strategies is the optimal solution playing as P2! Have fun ~~bullying your friends~~! `^.^`
