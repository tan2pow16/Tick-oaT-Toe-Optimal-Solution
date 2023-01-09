# Tick-oaT-Two Optimal Solution

Strategy for P2 derived by `Tangent65536`.

A friend of mine told me about a small game they came across on YouTube. Turns out it's really fun and there is an optimal solution when playing as the 2<sup>nd</sup> player! Maybe you should try to figure that out on your own first, and see if your solution works! `^.^`  

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

---

## Observations
### Spoiler alert! Try the game before reading this!
Here are the answers to the questions above.  
1. You may fill in at most 6 slots without forming any line. (Keep in mind that it has both rotational and mirror symmetry)
```
-------------
| + | + |   |
-------------
| + |   | + |
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

The key point lies in that at a certain point, the 1<sup>st</sup> player (P1) will have to fill in 7 slots before the 2<sup>nd</sup> player (P2). As P2 can decide which slot to fill in _after_ P1, the optimal solution for P2 is to follow the pattern above while leaving at least 1 slot filled in by P1 in the "void diagonal". Following this rule, one may derive the first set of strategies playing as P2:

1. **Do NOT touch the center before completing the mentioned pattern.**
2. **Do NOT claim any corner before P1.**
3. **When P1 claims their first corner, claim a neighboring corner and leave the corner slot claimed by P1 in the "void diagonal". Now your target pattern is fixed.**

Note that for P1 to _not_ complete any line at this point before filling the 7th slot, they must have claimed the 2 corner slots in P2's "void diagonal". This makes the tailgating lock obsolete, as in this specific case, any move proposed by P1 will guarantee that P2 can complete a line P1 has already finished, defeating P1.  

Example:
```
-------------
| 2 | 3 | 1 |
-------------
| 3 | 0 | 3 |    P1's turn! P2 has already won! Can you see why? ^.^
-------------
| 1 | 7 | 2 |
-------------
```

However, there's a minor catch -- as P2, you cannot just randomly fill in a target slot according to the given pattern. In a specific case, P2 may run into the scenario that P1 locked the last slot required to finish the required pattern, forcing P2 to complete a line.

Example:
```
-------------
| 1 | 3 | 2 |
-------------    P2's turn!
| 3 | 0 | 3 |    However, the bottom-left corner cannot be claimed by P2 now.
-------------    P1 has already won! Oof!
| 5 | 2 | 1 |
-------------
```

Note that when the required pattern is complete, since `(6 + 6) % 9 == 3`, there will always be at least 3 co-claimed slots. As a result, P2 can always avoid getting locked out by "preserving" a slot in the required pattern claimed 2 steps ahead by P1. Always choose one to preserve unless it's obvious that P1 cannot lock you out. That's rule no. 4.  

4. **If the required pattern is determined, claim the empty slots in the pattern first.**

If you're keen on minor details, you may already have a question: _what if P1 also claims the 4 edge-center pieces before touching any corner?_  

Good question! In this case, the tailgate lock becomes a double-edged sword. If you followed rule no. 3, _you'll lose due to getting locked out by P1!_ However, if you claim the diagonal corner that P1 just claimed as their 5th slot, P1 is now deadlocked and must claim either the center slot or a neighboring slot. You as P2 can still finish the required pattern as the diagonal slot claimed by P1 will be unlocked when you need it. This is rule no. 5.  

5. **Given the following scenario, claim the diagonal slot marked by the x (in the following example; its value is 0 at the point). This overrides rule 3.!**
```
-------------
| 0 | 3 | 5 |
-------------    P2's turn!
| 3 | 0 | 3 |    Claim the slot marked as 'x'! That locks out P1 from winning!
-------------    After P2's move, the tailgate lock (5) at this moment is unlocked!
| x | 3 | 0 |
-------------
```

This is the optimal solution playing as P2! Have fun! `^.^`
