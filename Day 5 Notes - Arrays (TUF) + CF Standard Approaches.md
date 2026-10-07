# Day 5 Notes: Arrays (TUF) + CF Standard Approaches

Shashank ke liye, simple Hinglish mein. Har topic: intuition, approach, code, complexity, aur galti jo avoid karni hai.

## 1. Pascal's Triangle (I, II, III)

**Core idea:** Row n (0-indexed) ka k-th element = C(n, k). Agla element pichle se nikalta hai:

`next = prev * (n - k) / (k + 1)`

Division hamesha exact hota hai, kyunki `prev * (n-k)` consecutive numbers ka product hota hai jo `(k+1)!` se divisible hai. Isliye **pehle multiply, phir divide** karo (ulta karoge to decimal/galat answer).

| Variant | Kya poochta hai | Optimal | Time | Space |
| --- | --- | --- | --- | --- |
| I | Row r, col c ka ek element | C(r-1, c-1) iterative | O(min(c, r-c)) | O(1) |
| II | Poori r-th row | formula se ek ek element | O(r) | O(1) extra |
| III | Pehli n rows | har row II wali method se | O(n^2) | O(n^2) |

**Pascal I**

```plain
long long nCr(int n, int r) {
    if (r > n - r) r = n - r;      // C(n,r) = C(n,n-r), kam iterations
    long long res = 1;
    for (int i = 0; i < r; i++) {
        res = res * (n - i) / (i + 1);
    }
    return res;
}
// answer = nCr(r - 1, c - 1)
```

**Pascal II** (r-th row, 1-indexed)

```plain
vector<long long> getRow(int r) {
    vector<long long> ans(r);
    ans[0] = 1;
    for (int i = 1; i < r; i++)
        ans[i] = ans[i-1] * (r - i) / i;
    return ans;
}
```

**Pascal III**

```cpp
vector<vector<long long>> pascal(int n) {
    vector<vector<long long>> t;
    for (int row = 1; row <= n; row++) t.push_back(getRow(row));
    return t;
}
```

**Galtiyan jo TUF ke code mein thi (dhyan rakho):**

- `int` overflow: `res * (n-i)` int mein jaldi overflow karta hai (row 17-18 ke baad). Hamesha `long long`.
- Pascal II mein bhi `ans[i-1]*(r-i)` int mein overflow karega.
- `r == 1` wala base case zaroori nahi, loop khud handle karta hai.

## 2. Rotate Matrix 90 degrees (clockwise)

**Intuition:** Transpose karo (rows <-> columns), phir har row reverse karo. Extra space nahi.

```cpp
void rotateMatrix(vector<vector<int>>& m) {
    int n = m.size();
    for (int i = 0; i < n; i++)
        for (int j = 0; j < i; j++)      // sirf half, j < i
            swap(m[i][j], m[j][i]);
    for (int i = 0; i < n; i++)
        reverse(m[i].begin(), m[i].end());
}
```

- Time O(N^2), Space O(1).
- **Classic bug:** transpose mein `j < n` kar diya to har pair do baar swap hoke wapas original ho jata hai. `j < i` hi rakho.
- **Anti-clockwise:** transpose ke baad rows ka order reverse karo: `reverse(m.begin(), m.end())`.
- TUF ke main() mein `sol.rotate(arr)` likha tha, function ka naam `rotateMatrix` hai. Compile error aayega.

## 3. Two Sum

- **Brute:** do loops, O(n^2).
- **Optimal (index chahiye):** hash map. Har x ke liye dekho `target - x` pehle aaya hai kya, phir x ko map mein daalo. O(n) time, O(n) space.
- **Sirf YES/NO chahiye:** sort + two pointers (l=0, r=n-1; sum chhota to l++, bada to r--). O(n log n), O(1) space.

```cpp
unordered_map<int,int> mp;
for (int i = 0; i < n; i++) {
    if (mp.count(target - a[i])) return {mp[target - a[i]], i};
    mp[a[i]] = i;
}
```

Pehle check, baad mein insert: isse ek hi element do baar use nahi hota.

## 4. 3 Sum

**Intuition:** Sort karo. Ek element i fix karo, baaki do ke liye Two Sum (two pointers) chalao. Duplicates skip karo taaki triplet repeat na ho.

```cpp
vector<vector<int>> threeSum(vector<int>& a) {
    sort(a.begin(), a.end());
    int n = a.size();
    vector<vector<int>> res;
    for (int i = 0; i < n; i++) {
        if (i > 0 && a[i] == a[i-1]) continue;
        int j = i + 1, k = n - 1;
        while (j < k) {
            long long s = (long long)a[i] + a[j] + a[k];
            if (s < 0) j++;
            else if (s > 0) k--;
            else {
                res.push_back({a[i], a[j], a[k]});
                j++; k--;
                while (j < k && a[j] == a[j-1]) j++;
                while (j < k && a[k] == a[k+1]) k--;
            }
        }
    }
    return res;
}
```

Time O(n^2), Space O(1) (answer chhod ke). Brute O(n^3) hota hai.

## 5. 4 Sum

Same idea, ek loop aur: sort, i aur j fix karo, baaki do pointers se. **i aur j dono ke duplicates skip karo.** Sum hamesha `long long` mein (4 numbers 1e9 ke add hone par int overflow).

Time O(n^3), Space O(1). Pattern yaad rakho: **k-Sum = (k-2) nested loops + two pointers.**

## 6. Sort 0, 1, 2 (Dutch National Flag)

**Intuition:** Teen pointers: `lo`, `mid`, `hi`. Invariant:

- `[0, lo)` sab 0
- `[lo, mid)` sab 1
- `(hi, n)` sab 2
- `[mid, hi]` abhi unknown

```cpp
int lo = 0, mid = 0, hi = n - 1;
while (mid <= hi) {
    if (a[mid] == 0) swap(a[lo++], a[mid++]);
    else if (a[mid] == 1) mid++;
    else swap(a[mid], a[hi--]);   // mid yahan badhta NAHI
}
```

2 ke case mein mid isliye nahi badhta kyunki `hi` se jo element aaya wo unknown hai, use check karna baaki hai. Time O(n), Space O(1), ek hi pass.

## 7. Kadane's Algorithm (max subarray sum)

**Intuition:** Har position par decide karo: pichla subarray aage badhao ya yahin se naya shuru karo. Agar pichla sum negative hai to wo bojh hai, naya shuru karo.

```cpp
long long best = a[0], cur = 0;
for (int x : a) {
    cur = max((long long)x, cur + x);
    best = max(best, cur);
}
```

- Time O(n), Space O(1).
- **All-negative case:** `best` ko 0 se initialize mat karo, `a[0]` ya `-INF` se karo.
- Subarray bhi print karna ho to `cur` reset hone par `start` update karo, `best` improve hone par `(ans_start, ans_end)` save karo.

---

# Codeforces: Standard Approaches (solved problems)

## CF 1373B: 01 Game

**Problem:** Binary string. Alice aur Bob baari baari se do adjacent **alag** characters ("01" ya "10") hatate hain. Alice pehle. Jo move nahi kar sake wo haarta hai. Winner batao (output `DA` = Alice jeetega, `NET` = nahi).

**Key observation:** Har move mein ek 0 aur ek 1 hatta hai. Jab tak dono bache hain, adjacent alag pair hamesha milta hai. Isliye total moves = `min(count0, count1)`.

- Moves odd -> Alice aakhri move karti hai -> `DA`
- Moves even -> `NET`

```cpp
int c0 = count(s.begin(), s.end(), '0');
int c1 = s.size() - c0;
cout << (min(c0, c1) % 2 ? "DA" : "NET") << "\n";
```

O(n). Alternative: stack simulation, par formula simple hai.

**Tumhare submissions:** 1 CE, 1 RE, 4 WA (WA on test 1 teen baar). Test 1 matlab sample hi fail. Output strings exact check karo (`DA`/`NET`, uppercase) aur sample locally chalao.

## CF 1360C: Similar Pairs

**Problem:** Even length array. Do numbers "similar" hain agar same parity ho **ya** unka difference exactly 1 ho. Kya poora array similar pairs mein todha ja sakta hai?

**Key observation:**

1. Count karo kitne odd hain.
2. Agar odd count **even** hai, to evens bhi even honge: odd-odd aur even-even pairs bana lo. **YES**.
3. Agar odd count **odd** hai, to evens bhi odd honge. Tab kam se kam ek (odd, even) pair banana padega, jo tabhi valid hai jab unka diff exactly 1 ho. Baaki bache hue sab even-even / odd-odd ban jayenge.
4. Isliye: sort karo, dekho koi adjacent pair ka diff 1 hai kya. Hai to **YES**, warna **NO**.

```cpp
sort(a.begin(), a.end());
int odd = 0;
for (int x : a) odd += x & 1;
bool ok = (odd % 2 == 0);
for (int i = 0; i + 1 < n && !ok; i++)
    if (a[i+1] - a[i] == 1) ok = true;
cout << (ok ? "YES" : "NO") << "\n";
```

O(n log n). **Tumhare submissions:** 2 CE, 3 WA. Case 3 (odd count odd) pe galti hone ki sambhavna zyada hoti hai.

## Pehle ke solved problems (one-liners)

| Problem | Standard idea |
| --- | --- |
| 1339A Filling Diamonds | Answer sirf `n`. Output ke baad newline mat bhoolna. |
| 1360B Honest Coach | Sort karo, adjacent differences ka minimum. |
| 1385B Restore the Permutation by Merger | Har number ka sirf pehla occurrence print karo (set/visited array). |
| 1399A Remove Smallest | Sort karo; koi adjacent diff > 1 ho to NO, warna YES. |
| 1692B All Distinct | `d` = distinct count. `(n - d)` even to answer d, odd to d - 1. |

---

# Submit karne se pehle checklist (WA on test 1 aur CE rokne ke liye)

1. Sample input file se local run karo, output ko sample output se compare karo.
2. Output format: `YES/NO` ka case, `DA/NET`, newline, extra space.
3. Multi-test problem mein arrays/variables har test ke start par reset hue ya nahi.
4. Overflow: sum/product ho to `long long`.
5. Indexing: 0-based vs 1-based, loop bounds `i+1 < n`.
6. Ek chhota edge case khud banao (n minimum, sab same, sab negative).
7. Tab hi Submit dabao. Rated contest mein har WA ka 10 min penalty hota hai.
