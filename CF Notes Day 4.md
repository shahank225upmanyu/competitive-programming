# CF Notes: Day 4 (5 Oct 2026)

Handle: bull05 | Approaches: frequency counting, parity, index placement

---

## 1. 1669B Triple (800)

**Problem:** Array mein koi value kam se kam **3 baar** aati hai to wo value print karo, warna `-1`.

**Approach (frequency counting):**

1. Values `1..n` ke beech hain, to `cnt[n+1]` array bana lo.
2. Har element ke liye `cnt[x]++`. Jaise hi `cnt[x] >= 3` ho, wahi answer hai.
3. Koi nahi mila to `-1`.

**Complexity:** O(n)

```cpp
vector<int> cnt(n + 1, 0);
int ans = -1;
for (int i = 0; i < n; i++) {
    int x; cin >> x;
    if (++cnt[x] >= 3) ans = x;
}
cout << ans << "\n";
```

**Alternative:** sort karo aur check karo `a[i] == a[i+2]`.

**Yaad rakhne ki baat:** `cnt` ko har test case mein naya banao.

---

## 2. 1692B All Distinct (800)

**Problem:** Ek operation mein exactly 2 elements hatate hain. Final array mein sab distinct chahiye. Max size batao.

**Approach (parity):**

1. `d` = distinct elements ka count (`set` se).
2. Duplicates `n - d` hain, unhe hatana zaroori hai.
3. Har operation 2 elements hatata hai, isliye final size ki parity hamesha `n` ki parity jaisi rehti hai.
4. Agar `(n - d)` **even** hai to answer `d`.
5. Agar `(n - d)` **odd** hai to ek extra removal chahiye, jo ek distinct element ko bhi hata dega. Answer `d - 1`.

**Complexity:** O(n log n)

```cpp
set<int> s;
for (int i = 0; i < n; i++) { int x; cin >> x; s.insert(x); }
int d = s.size();
if ((n - d) % 2 == 0) cout << d << "\n";
else cout << d - 1 << "\n";
```

**Check:** `9 1 9 9 1`: n=5, d=2, n-d=3 (odd), answer = 1.

**Mistake jo hui:** distinct count seedha answer samajh liya. Parity ka angle bhool gaye.

---

## 3. 1367B Even Array (800)

**Problem:** Har index `i` pe `i % 2 == a[i] % 2` hona chahiye. Ek swap mein koi bhi 2 elements swap kar sakte ho. Minimum swaps batao, ya `-1`.

**Approach (index parity):**

1. `x` = even index pe odd value wale elements.
2. `y` = odd index pe even value wale elements.
3. Ek swap ek "even index pe odd" aur ek "odd index pe even" element ko saath mein theek karta hai.
4. Agar `x == y` to answer `x`, warna `-1`.

**Complexity:** O(n), koi swap simulate nahi karna.

```cpp
int x = 0, y = 0;
for (int i = 0; i < n; i++) {
    int a; cin >> a;
    if (i % 2 != a % 2) {
        if (i % 2 == 0) x++;
        else y++;
    }
}
cout << (x == y ? x : -1) << "\n";
```

**Mistakes jo hui:**

- Swap simulate kiya, `while` mein `k++` bhool gaye (TLE).
- `p = 0` se start kiya, jabki 0 valid index hai. Sentinel `-1` hona chahiye.
- Count 0 ko `-1` samjha. `0` swaps ka matlab "pehle se theek hai".
- Even/odd values ki count barabar maani, jo sirf even `n` ke liye sach hai.

---

## 4. 1669C Odd/Even Increments (800)

**Problem:** Ek operation mein ya to saare odd positions (1-indexed) ke elements `+1` karte hain, ya saare even positions ke. Kya sab elements ki parity same ho sakti hai?

**Approach (index placement):**

1. Ek operation ek poore group ko `+1` karta hai, to group ke andar ke elements ki parity ka relation kabhi nahi badalta.
2. Isliye zaroori hai ki **odd positions ke saare elements ki parity same ho**, aur **even positions ke saare elements ki parity same ho**.
3. Ye ho to dono groups ko apni marzi se flip karke same parity pe laa sakte ho. Answer `YES`, warna `NO`.

**Complexity:** O(n)

```cpp
vector<int> a(n);
for (auto &x : a) cin >> x;
bool ok = true;
for (int i = 0; i < n; i++)
    if (a[i] % 2 != a[i % 2] % 2) ok = false;
cout << (ok ? "YES" : "NO") << "\n";
```

**Yaad rakhne ki baat:** `i % 2` se index group hota hai. Constraint `n >= 2` hai.

**Status:** screenshot mein is problem ka koi `Accepted` nahi dikha. Pehle ek baar solve karke AC lao, phir notes final maano.

---

## Meri galtiyon ka log (har hafte padho)

| Galti | Fix |
| --- | --- |
| `while` mein counter (`k++`) bhool jaana | while likhte hi counter ka increment sabse pehle likho |
| Sample bina chalaye submit karna | Local mein sample run, phir submit |
| `vector<char> arr[n]` likhna | Size ke liye `vector<char> arr(n)`, ya string use karo |
| `size() - 1` pe unsigned underflow | `int n = arr.size();` pehle le lo |
| Sentinel `0` jo valid index bhi hai | Sentinel `-1` rakho |
| `-1` aur `0` answer ko mix karna | Har special answer ka alag condition likho |
| Formula chhote case pe bana ke submit karna | 3-4 chhote cases haath se check karo |
| `-1` print karke `continue` na lagana | Output ke baad flow rok do |

## Submit se pehle 30-second checklist

1. Sample input/output match hua?
2. `n = 1` aur chhote cases haath se chalaye?
3. Har loop ka counter badh raha hai?
4. Arrays/sets har test case mein naye hain?
5. Kya swap/erase simulate kiye bina counting se ho sakta hai?