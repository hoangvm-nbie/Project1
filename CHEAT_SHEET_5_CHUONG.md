# Cheat sheet thực chiến - Thi 4 bài code Thuật toán ứng dụng

**29/11/2026 | C++17 | Gặp bài → lấy mẫu → sửa input/output → test → submit.**

**Cách trình bày:** ưu tiên trạng thái và cách giải trong slide; hàm thường, biến rõ nghĩa, tách từng bước. Các phần ngoài slide được ghi là bổ sung. Không chép lại những lỗi giả mã đã nhận ra trong slide.

Mỗi mục dưới đây có dấu hiệu nhận dạng, ví dụ và code. Các mẫu dùng chung khung mục 1: **biến toàn cục và hàm đặt trước main; đoạn đọc/xử lý/in đặt trong main**. Mỗi bài là một file riêng, không ghép tất cả mẫu vào một chương trình. Tên n, build, solve có thể lặp giữa các bài vì chúng nằm ở các file riêng. Khi đã khai báo biến toàn cục, trong main chỉ đọc/gán biến, không khai báo lại biến cùng tên. Input minh họa phải sửa cho khớp đề thi.

## 0. Đọc đề xong, tìm đúng dòng này

| Đề hỏi gì? | Mục | Lưu ý |
|---|---|---|
| Cộng số cực nhiều chữ số | 2 | Dùng string |
| Thêm/xóa/thống kê tần suất | 3 | Chọn container |
| Số nhỏ nhất lớn hơn k | 4 | upper_bound |
| Ghép hai vật, tổng phí ít nhất | 5 | Min-heap |
| Tổng đoạn/hình chữ nhật | 6 | Prefix sum |
| Đếm cặp tổng Q | 7 | Có trùng vẫn phải đếm đủ |
| Đoạn dài nhất tổng ≤ Q | 8 | Mọi số không âm |
| Min đoạn không sửa mảng | 9 | Sparse table |
| Min đoạn có gán lại phần tử | 10 | Segment tree |
| Xe buýt đón trả khách | 11 | Đón trước trả, tải ≤ K |
| K xe tải giao hàng | 12 | Tối đa hay đúng K xe? |
| Xếp hình chữ nhật | 13 | Có cho xoay không? |
| Nhiều công việc không chồng nhau nhất | 14 | Kết thúc sớm trước |
| Đổi tiền ít đồng nhất | 15 | Hệ bất kỳ dùng DP |
| Chọn đồ vào túi | 16 | Một lần/vô hạn/cắt lẻ? |
| Sắp xếp/trộn | 17 | sort/merge sort |
| Đoạn liên tiếp tổng lớn nhất | 18 | DP có nhớ / chia để trị |
| Ngưỡng nhỏ nhất/lớn nhất làm được | 19 | Chặt nhị phân đáp án |
| a^n mod m | 20 | Lũy thừa nhanh |
| Fibonacci / ký tự xâu Fibonacci | 21 | Đúng thứ tự ghép |
| Dãy con tăng dài nhất | 22 | LIS |
| Dãy con chung dài nhất | 23 | LCS |
| Qua mọi thành phố rồi về đầu | 24 | TSP bitmask |

Trước khi code, xác định: **Output là gì? Chỉ số từ 0 hay 1? Có số âm/trùng? Một test hay nhiều test?** Sau khi code, chạy sample và ít nhất một test tự tính tay. Không in lời nhắc nhập dữ liệu.

## 1. Khung file submit

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

// Bien toan cuc va ham dat o day.

int main(){
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    // Doc input, xu ly, in output.
    return 0;
}
```

- Tổng, số cặp, chi phí: dùng `ll`. Nhân int phải ép trước: `ll p=1LL*a*b;`.
- Tổng vector: `accumulate(a.begin(),a.end(),0LL)`.
- Vector 2D: `vector<vector<int>> a(n,vector<int>(m,0));`.
- Muốn truy cập a[i], phải tạo phần tử bằng constructor/resize; reserve chưa tạo phần tử.
- Có T test mới đọc T; reset dữ liệu từng test.
- Số thực: `cout<<fixed<<setprecision(6)<<ans;`, sửa 6 theo đề.

## 2. Cộng số nguyên lớn không âm

Ví dụ `99999999999999999999 + 1` → `100000000000000000000`. Đặt hàm trước main:

```cpp
string addBig(string a,string b){
    int i=(int)a.size()-1,j=(int)b.size()-1,carry=0;
    string s;
    while(i>=0 || j>=0 || carry){
        int t=carry;
        if(i>=0)t+=a[i--]-'0';
        if(j>=0)t+=b[j--]-'0';
        s.push_back(char('0'+t%10));carry=t/10;
    }
    reverse(s.begin(),s.end());
    auto p=s.find_first_not_of('0');
    return p==string::npos?"0":s.substr(p);
}
```

Hai số: `string a,b;cin>>a>>b;cout<<addBig(a,b);`.

**Integer Inquiry:** nhiều số lớn, gặp `0` thì dừng:

```cpp
string s,total="0";
while(cin>>s && s!="0")total=addBig(total,s);
cout<<total;
```

Test: 0+0; 999+1; khác độ dài. Mẫu chưa xử lý dấu âm. Không dùng double thay số nguyên lớn cần chính xác.

## 3. Thống kê / mô phỏng: chọn container

| Cần làm | Dùng | Lệnh |
|---|---|---|
| Mảng động | vector | push_back, pop_back, a[i] |
| Hai đầu | deque | push_front/back, pop_front/back |
| Vào sau ra trước | stack | push, top, pop |
| Vào trước ra trước | queue | push, front, pop |
| Lấy lớn/nhỏ nhất liên tục | priority_queue | push, top, pop |
| Đếm tần suất | map/unordered_map | freq[x]++ |
| Kiểm tra tồn tại | set/unordered_set | insert, count, find |
| Có thứ tự và cho trùng | multiset | insert, find, erase(iterator) |
| Chèn/xóa/chuyển nút | list | insert, erase, splice |

**Hai danh sách:** xóa ít phần tử nhất để hai danh sách chứa cùng số lần xuất hiện mỗi giá trị, không cần cùng thứ tự. A=`1 2 3 2 1`, B=`1 2 5 2 3` → **2**. Input n m, rồi hai danh sách:

```cpp
int n,m;cin>>n>>m;
map<ll,int> diff;
for(int i=0;i<n;++i){ll x;cin>>x;++diff[x];}
for(int i=0;i<m;++i){ll x;cin>>x;--diff[x];}
ll ans=0;
for(auto [x,c]:diff)ans+=abs(c);
cout<<ans;
```

Chỉ thống kê một danh sách: `freq[x]++`, duyệt `for(auto [x,c]:freq)`. Map duyệt key tăng; unordered_map không giữ thứ tự.

Các thao tác sửa theo bài:

```cpp
v.insert(v.begin()+pos,x); // chen truoc pos
v.erase(v.begin()+pos);
v.erase(v.begin()+l,v.begin()+r); // xoa [l,r), khong xoa r
sort(v.begin(),v.end());
v.erase(unique(v.begin(),v.end()),v.end()); // bo trung sau sort
```

- `pop()` không trả giá trị: đọc top/front rồi pop; kiểm tra empty trước.
- `find(v.begin(),v.end(),x)` trả iterator; không có trả end. Chỉ số vector là `it-v.begin()`.
- `find_if` tìm theo điều kiện; `min_element/max_element` trả iterator, lấy `*it` để được giá trị. Không dereference end.
- `m.insert({k,v})` không ghi đè; `m[k]=v` ghi đè và có thể tạo key.
- `multiset.erase(x)` xóa mọi bản sao. Xóa một: `auto it=s.find(x);if(it!=s.end())s.erase(it);`.
- List dùng `L.sort()`. `unique()` chỉ bỏ trùng kề nhau; `merge` cần cả hai list đã sort cùng comparator; `splice` chuyển nút.
- Comparator sort dùng `<`/`>`, không `<=`. Duyệt ngược dùng rbegin đến rend.
- Liệt kê mọi hoán vị: sort rồi `do { /* xu ly */ } while(next_permutation(v.begin(),v.end()));`.
- Stack/queue không có iterator công khai. Muốn duyệt giữ nguyên thì copy trước.

## 4. Số nhỏ nhất lớn hơn k

a=`1 3 3 7`, k=3 →7; k=7 →-1. Input n, mảng, các dòng `next k`, kết thúc `#`:

```cpp
int n;cin>>n;vector<ll>a(n);for(ll &x:a)cin>>x;
sort(a.begin(),a.end());
string cmd;
while(cin>>cmd && cmd!="#"){
    ll k;cin>>k;
    auto it=upper_bound(a.begin(),a.end(),k);
    cout<<(it==a.end()?-1:*it)<<'\n';
}
```

Đầu tiên ≥k: lower_bound; >k: upper_bound; đếm k: hiệu upper_bound-lower_bound; kiểm tra có k: binary_search. Mảng phải sort. Có thêm/xóa xen kẽ thì dùng set/multiset và `s.upper_bound(k)`.

## 5. Ghép hàng/Robot: lấy hai vật nhỏ nhất

`1 2 3 4`: ghép 1+2=3, 3+3=6, 4+6=10, tổng **19**.

```cpp
int n;cin>>n;
priority_queue<ll,vector<ll>,greater<ll>>pq;
for(int i=0;i<n;++i){ll x;cin>>x;pq.push(x);}
ll total=0;
while(pq.size()>1){
    ll a=pq.top();pq.pop();ll b=pq.top();pq.pop();
    total+=a+b;pq.push(a+b);
}
cout<<total;
```

Robot slide: phí 5%, lấy phần nguyên **tổng phí** → `cout<<total/20;`. Không chia 20 từng lượt. Ví dụ trên phí phần nguyên là 0. Lấy lớn nhất: `priority_queue<ll>pq;`.

## 6. Tổng đoạn / hình chữ nhật

### 6A. Tổng [l,r], chỉ số từ 1

Input n, mảng, q, q cặp l r. `2 -1 4`, query 2 3 →**3**.

```cpp
int n;cin>>n;vector<ll>p(n+1,0);
for(int i=1;i<=n;++i){ll x;cin>>x;p[i]=p[i-1]+x;}
int q;cin>>q;
while(q--){int l,r;cin>>l>>r;cout<<p[r]-p[l-1]<<'\n';}
```

Số âm vẫn đúng; đề từ 0 thì tăng l,r thêm 1. Có cập nhật thì prefix cũ không còn đúng.

### 6B. Hình chữ nhật

Input n m, ma trận, q, các query u v x y: góc trên trái và dưới phải, từ 1.

```cpp
int n,m;cin>>n>>m;
vector<vector<ll>>p(n+1,vector<ll>(m+1,0));
for(int i=1;i<=n;++i)for(int j=1;j<=m;++j){
    ll x;cin>>x;
    p[i][j]=x+p[i-1][j]+p[i][j-1]-p[i-1][j-1];
}
int q;cin>>q;
while(q--){
    int u,v,x,y;cin>>u>>v>>x>>y;
    cout<<p[x][y]-p[u-1][y]-p[x][v-1]+p[u-1][v-1]<<'\n';
}
```

Ma trận `1 2 3 / 4 5 6 / 7 8 9`, query 2 2 3 3 →**28**.

## 7. Đếm cặp tổng Q

Input n Q, n số. Đếm cặp **chỉ số i<j**, cả khi có trùng:

```cpp
int n;ll Q;cin>>n>>Q;
unordered_map<ll,ll>freq;ll ans=0;
for(int i=0;i<n;++i){
    ll x;cin>>x;
    auto it=freq.find(Q-x);
    if(it!=freq.end())ans+=it->second;
    ++freq[x];
}
cout<<ans;
```

`1 1 2 2`, Q=3 →**4**; `3 3 3`, Q=6 →**3**. Thêm x sau khi đếm để không dùng một phần tử hai lần.

Nếu các số đôi một khác nhau: sort rồi hai con trỏ:

```cpp
sort(a.begin(),a.end());int l=0,r=(int)a.size()-1;ll ans=0;
while(l<r){
    ll s=a[l]+a[r];
    if(s==Q){++ans;++l;--r;}
    else if(s<Q)++l;else --r;
}
```

Cần chỉ số gốc: lưu value,index. Đếm cặp **giá trị phân biệt** khác đếm cặp chỉ số, không cộng nguyên tần suất như trên.

## 8. Đoạn dài nhất tổng ≤Q, mọi số không âm

Input n Q, mảng. `2 1 3 1`, Q=4 →**2**.

```cpp
int n;ll Q;cin>>n>>Q;vector<ll>a(n);for(ll &x:a)cin>>x;
int l=0,ans=0;ll sum=0;
for(int r=0;r<n;++r){
    sum+=a[r];
    while(l<=r && sum>Q)sum-=a[l++];
    if(sum<=Q)ans=max(ans,r-l+1);
}
cout<<ans;
```

Cần xuất đoạn: lưu l,r khi ans tăng, đổi chỉ số nếu cần. **Có số âm không dùng**: `[5,-4]`, Q=1 có đoạn dài 2 nhưng bỏ 5 sớm sẽ sai.

## 9. Min đoạn không cập nhật: sparse table

Bản đầy đủ như mẫu bạn gửi: biến toàn cục, hàm build/getMin, rồi main. Input n≤100000, mảng, q và q cặp l r từ **0**. Bài trong slide yêu cầu **tổng các min**, nên cuối chỉ in total.

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

const int MAXN = 100005;
const int LOG = 18;
int n;
ll st[LOG][MAXN];

void build(const vector<ll>& a) {
    for (int i = 0; i < n; ++i) st[0][i] = a[i];
    for (int k = 1; (1 << k) <= n; ++k) {
        for (int i = 0; i + (1 << k) <= n; ++i) {
            st[k][i] = min(st[k-1][i], st[k-1][i+(1 << (k-1))]);
        }
    }
}

ll getMin(int l, int r) {
    int k = __lg(r - l + 1);
    return min(st[k][l], st[k][r - (1 << k) + 1]);
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n;
    vector<ll> a(n);
    for (int i = 0; i < n; ++i) cin >> a[i];
    build(a);
    int q;
    cin >> q;
    ll total = 0;
    while (q--) {
        int l, r;
        cin >> l >> r;
        // Neu input tu 1: --l; --r;
        total += getMin(l, r);
    }
    cout << total << '\n';
    return 0;
}
```

`9 2 6 3 1 5 7`, hai query [1,3] và [4,6] →2+1=**3**. Nếu đề in riêng từng min: thay dòng cộng total bằng `cout << getMin(l,r) << '\n';` và bỏ dòng in total cuối. MAXN là sức chứa số phần tử; LOG là số tầng: n lớn hơn thì tăng cả hai phù hợp. Không gọi query đoạn rỗng. Cần vị trí min thì lưu thêm chỉ số; không đổi min thành cộng trong cách query này vì có vùng chồng.

## 10. Gán điểm + min đoạn: segment tree

Biến toàn cục và hàm đặt trước main. Mẫu dùng **chỉ số từ 1** cho cả mảng và input, n≤100000. `update` là gán a[p]=value.

```cpp
const int MAXN = 100005;
int n;
ll a[MAXN], tree[4 * MAXN];

void build(int id, int l, int r) {
    if (l == r) {
        tree[id] = a[l];
        return;
    }
    int mid = (l + r) / 2;
    build(id * 2, l, mid);
    build(id * 2 + 1, mid + 1, r);
    tree[id] = min(tree[id * 2], tree[id * 2 + 1]);
}

void update(int id, int l, int r, int p, ll value) {
    if (l == r) {
        a[p] = value;
        tree[id] = value;
        return;
    }
    int mid = (l + r) / 2;
    if (p <= mid) update(id * 2, l, mid, p, value);
    else update(id * 2 + 1, mid + 1, r, p, value);
    tree[id] = min(tree[id * 2], tree[id * 2 + 1]);
}

ll getMin(int id, int l, int r, int u, int v) {
    if (r < u || v < l) return LLONG_MAX;
    if (u <= l && r <= v) return tree[id];
    int mid = (l + r) / 2;
    return min(getMin(id * 2, l, mid, u, v),
               getMin(id * 2 + 1, mid + 1, r, u, v));
}
```

Trong main: n, mảng, q; q dòng update p x hoặc get-min l r:

```cpp
cin >> n;
for (int i = 1; i <= n; ++i) cin >> a[i];
build(1, 1, n);
int q;
cin >> q;
while (q--) {
    string op;
    cin >> op;
    if (op == "update") {
        int p;
        ll value;
        cin >> p >> value;
        update(1, 1, n, p, value);
    } else {
        int l, r;
        cin >> l >> r;
        cout << getMin(1, 1, n, l, r) << '\n';
    }
}
```

`9 2 6 3 1 5 7`: get-min 1 6 →1; update 5 8; get-min 1 6 →2. Nếu input từ 0 thì tăng chỉ số thêm 1 trước khi gọi hàm. Tổng đoạn: gộp bằng cộng, không giao trả 0. Max đoạn: gộp max, không giao trả LLONG_MIN. Cập nhật cả đoạn cần cách khác.

## 11. Xe buýt đón trả khách: CBUS

n khách; đón i, trả i+n; xe K chỗ, từ 0 và về 0, ngắn nhất. Chỉ đón khi còn chỗ, chỉ trả khi đã đón; đi đủ **2n điểm** mới về kho. Ma trận đầy đủ, chi phí không âm, tổng chi phí nhỏ hơn INF. Biến và hàm trước main:

```cpp
const ll INF = LLONG_MAX / 4;
int n, K, load;
ll cur, best, cmin;
vector<vector<ll>> c;
vector<int> used;

bool check(int v) {
    if (used[v]) return false;
    if (v <= n) return load < K;
    return used[v - n];
}

void Try(int k, int last) {
    for (int v = 1; v <= 2 * n; ++v) {
        if (!check(v)) continue;
        int delta = (v <= n ? 1 : -1);
        used[v] = 1;
        load += delta;
        cur += c[last][v];

        if (k == 2 * n) best = min(best, cur + c[v][0]);
        else if (cur + (2 * n + 1 - k) * cmin < best) Try(k + 1, v);

        cur -= c[last][v];
        load -= delta;
        used[v] = 0;
    }
}

ll solve() {
    if (n == 0) return 0;
    if (K <= 0) return -1;
    load = 0;
    cur = 0;
    best = cmin = INF;
    used.assign(2 * n + 1, 0);
    for (int i = 0; i <= 2 * n; ++i)
        for (int j = 0; j <= 2 * n; ++j)
            if (i != j) cmin = min(cmin, c[i][j]);
    Try(1, 0);
    return best == INF ? -1 : best;
}
```

Trong main: n K, ma trận (2n+1)×(2n+1):

```cpp
cin >> n >> K;
c.assign(2 * n + 1, vector<ll>(2 * n + 1));
for (int i = 0; i <= 2 * n; ++i)
    for (int j = 0; j <= 2 * n; ++j) cin >> c[i][j];
cout << solve();
```

Test n=1,K=1, ma trận `0 2 9 / 2 0 3 / 4 3 0` →**9**, tuyến 0→1→2→0. Muốn in tuyến: lưu đường đi và copy lúc best giảm. Không lấy đường chéo vào cmin, không dừng ở k=n, không quên hoàn tác load/used/cur.

**Bài quay lui khác:** chọn biến → thử giá trị hợp lệ → cập nhật → gọi tiếp → hoàn tác. Liệt kê cần giữ mọi nghiệm. Tìm min có thể bỏ nhánh bằng cận hợp lệ; không tự cắt cur>=best nếu phía sau có chi phí âm.

## 12. K xe tải giao hàng: CVRP

N khách, nhu cầu d[i], tải Q, mỗi xe từ kho 0 rồi về 0, mỗi khách một xe. Mẫu **tối đa K xe**, xe giống nhau, chi phí không âm, ma trận đầy đủ; tổng chi phí nhỏ hơn INF.

`y[k]` là khách đầu xe k, 0 là xe rỗng. Chọn đầu tuyến rồi nối các điểm sau. Các biến và hàm đặt trước main:

```cpp
const ll INF = LLONG_MAX / 4;
int n, K, segments, R;
ll Q, cur, best, cmin;
vector<ll> d, load;
vector<vector<ll>> c;
vector<int> used, y;

void TryRoute(int s, int k) {
    if (s == 0) {
        if (k < K) TryRoute(y[k + 1], k + 1);
        else if (segments == n + R) best = min(best, cur);
        return;
    }
    for (int v = 0; v <= n; ++v) {
        if (v > 0 && (used[v] || load[k] + d[v] > Q)) continue;
        cur += c[s][v];
        ++segments;
        if (v > 0) {
            used[v] = 1;
            load[k] += d[v];
        }

        if (v == 0 && k == K) {
            if (segments == n + R) best = min(best, cur);
        } else if (cur + (n + R - segments) * cmin < best) {
            if (v > 0) TryRoute(v, k);
            else TryRoute(y[k + 1], k + 1);
        }

        if (v > 0) {
            used[v] = 0;
            load[k] -= d[v];
        }
        --segments;
        cur -= c[s][v];
    }
}

void TryFirst(int k) {
    int start = (y[k - 1] > 0 ? y[k - 1] + 1 : 0);
    for (int v = start; v <= n; ++v) {
        if (v > 0 && (used[v] || d[v] > Q)) continue;
        y[k] = v;
        if (v > 0) {
            used[v] = 1;
            load[k] += d[v];
            cur += c[0][v];
            ++segments;
        }

        if (k < K) TryFirst(k + 1);
        else {
            R = segments;
            TryRoute(y[1], 1);
        }

        if (v > 0) {
            --segments;
            cur -= c[0][v];
            load[k] -= d[v];
            used[v] = 0;
        }
    }
}

ll solve() {
    if (n == 0) return 0;
    if (K <= 0) return -1;
    ll demand = 0;
    for (int i = 1; i <= n; ++i) {
        if (d[i] > Q) return -1;
        demand += d[i];
    }
    if (demand > K * Q) return -1;
    segments = R = 0;
    cur = 0;
    best = cmin = INF;
    used.assign(n + 1, 0);
    y.assign(K + 1, 0);
    load.assign(K + 1, 0);
    for (int i = 0; i <= n; ++i)
        for (int j = 0; j <= n; ++j)
            if (i != j) cmin = min(cmin, c[i][j]);
    TryFirst(1);
    return best == INF ? -1 : best;
}
```

Trong main: n K Q, d[1..n], ma trận (n+1)×(n+1):

```cpp
cin >> n >> K >> Q;
d.assign(n + 1, 0);
for (int i = 1; i <= n; ++i) cin >> d[i];
c.assign(n + 1, vector<ll>(n + 1));
for (int i = 0; i <= n; ++i)
    for (int j = 0; j <= n; ++j) cin >> c[i][j];
cout << solve();
```

Test n=2,K=2,Q=3,d=`2 2`, mọi cạnh khác đỉnh giá 1 →**4**. Đổi Q=4 →**3** vì một xe đủ.

Đúng K xe không rỗng: trong TryFirst đổi start thành `int start = (y[k-1] > 0 ? y[k-1]+1 : 1);`. Không nghiệm mẫu trả -1, sửa theo đề. Không cộng cạnh 0→0 cho xe rỗng. Dùng cho dữ liệu nhỏ, không cho hàng nghìn khách.

## 13. Xếp/cắt chữ nhật 2D

PDF chỉ có tên bài, thiếu đề chi tiết. Khung bổ sung này dành cho xếp miếng vào W×H, tọa độ nguyên, song song cạnh, không chồng nhau. Không đủ cho bài bắt buộc đường cắt xuyên tấm (guillotine).

Thử từng miếng → hướng → vị trí → kiểm tra biên/giao → đặt → thử tiếp. Dùng các mảng riêng cho chiều rộng, chiều cao, tọa độ; đặt trước main:

```cpp
int W, H, n;
bool allowRotate;
vector<int> widthPiece, heightPiece;
vector<int> posX, posY, placedW, placedH;

bool canPlace(int k, int x, int y, int w, int h) {
    if (x + w > W || y + h > H) return false;
    for (int j = 0; j < k; ++j) {
        bool overlap = x < posX[j] + placedW[j]
                    && posX[j] < x + w
                    && y < posY[j] + placedH[j]
                    && posY[j] < y + h;
        if (overlap) return false;
    }
    return true;
}

bool TryPlace(int k) {
    if (k == n) return true;
    int turns = 1;
    if (allowRotate && widthPiece[k] != heightPiece[k]) turns = 2;
    for (int turn = 0; turn < turns; ++turn) {
        int w = widthPiece[k], h = heightPiece[k];
        if (turn == 1) swap(w, h);
        for (int x = 0; x + w <= W; ++x) {
            for (int y = 0; y + h <= H; ++y) {
                if (!canPlace(k, x, y, w, h)) continue;
                posX[k] = x;
                posY[k] = y;
                placedW[k] = w;
                placedH[k] = h;
                if (TryPlace(k + 1)) return true;
                // Lan thu sau ghi de vi tri k, chi kiem tra cac mieng j<k.
            }
        }
    }
    return false;
}

bool solve() {
    ll area = 0;
    for (int i = 0; i < n; ++i) area += 1LL * widthPiece[i] * heightPiece[i];
    if (area > 1LL * W * H) return false;
    posX.assign(n, 0);
    posY.assign(n, 0);
    placedW.assign(n, 0);
    placedH.assign(n, 0);
    return TryPlace(0);
}
```

Main minh họa: W H n, rồi n cặp chiều rộng/chiều cao, cho xoay:

```cpp
cin >> W >> H >> n;
widthPiece.resize(n);
heightPiece.resize(n);
for (int i = 0; i < n; ++i) cin >> widthPiece[i] >> heightPiece[i];
allowRotate = true; // false neu cam xoay
cout << (solve() ? 1 : 0);
```

Sửa thứ tự input và YES/NO hay 1/0 theo đề. Tấm 3×2 và hai miếng 2×1 →1. Chỉ dùng cách duyệt vị trí này cho dữ liệu nhỏ; tổng diện tích đủ không bảo đảm xếp được.

## 14. Chọn nhiều công việc không chồng nhau nhất

Input n, n cặp start finish. Sort **finish tăng**, lấy việc bắt đầu sau/đúng lúc việc trước kết thúc:

```cpp
int n;cin>>n;vector<pair<ll,ll>>jobs;
for(int i=0;i<n;++i){ll s,f;cin>>s>>f;jobs.push_back({f,s});}
sort(jobs.begin(),jobs.end());
ll last=LLONG_MIN;int ans=0;
for(auto [f,s]:jobs)if(s>=last){++ans;last=f;}
cout<<ans;
```

`(1,3),(2,5),(3,4),(4,7),(5,8),(7,9)` →**4**. Cấm tiếp giáp thì dùng >. Cần ID thì lưu thêm ID rồi đưa vào đáp án lúc chọn. Nếu tối đa **tiền thưởng**, không phải số việc, mẫu này không bảo đảm đúng. Không sort theo bắt đầu sớm hoặc thời lượng ngắn nhất.

## 15. Đổi tiền ít đồng nhất

### 15A. Mệnh giá bất kỳ: đệ quy có nhớ theo slide

Input m X, m mệnh giá dương, mỗi loại dùng vô hạn. **MinCoin(i,x)**: ít đồng nhất để đổi x chỉ dùng i loại đầu. Có hai lựa chọn: không dùng đồng D[i], hoặc lấy một đồng D[i] rồi đổi tiếp x-D[i].

Biến và hàm trước main:

```cpp
const int INF = 1000000000;
int m, X;
vector<int> D;
vector<vector<int>> mem;

int MinCoin(int i, int x) {
    if (x == 0) return 0;
    if (x < 0 || i == 0) return INF;
    if (mem[i][x] != -1) return mem[i][x];

    int result = MinCoin(i - 1, x); // Khong dung loai i.
    if (x >= D[i]) {
        int remaining = MinCoin(i, x - D[i]);
        if (remaining != INF) {
            result = min(result, remaining + 1);
        }
    }
    mem[i][x] = result;
    return result;
}

void Trace(int i, int x) {
    if (x == 0 || i == 0) return;
    if (x >= D[i] && MinCoin(i, x) == 1 + MinCoin(i, x - D[i])) {
        cout << D[i] << ' ';
        Trace(i, x - D[i]);
    } else {
        Trace(i - 1, x);
    }
}
```

Trong main:

```cpp
cin >> m >> X;
D.resize(m + 1);
for (int i = 1; i <= m; ++i) cin >> D[i];
mem.assign(m + 1, vector<int>(X + 1, -1));
int answer = MinCoin(m, X);
if (answer == INF) {
    cout << -1;
} else {
    cout << answer;
    // Neu doi in cac dong tien, them:
    // cout << '\n';
    // Trace(m, X);
}
```

`1 3 4`, X=6 →**2** (3+3); `7 8 9`, X=1 →**-1**; X=0 →0. Nhánh lấy đồng vẫn giữ i vì được dùng loại đó tiếp. Kiểm tra x=0 trước i=0; đã nhớ dùng !=-1, sửa lỗi dấu trong slide. Chỉ truy vết khi có nghiệm.

Nếu X lớn và có đồng nhỏ, đệ quy có thể quá sâu. Khi gặp giới hạn đó, tính cùng bảng bằng vòng lặp sau, đặt **thay cho đoạn gọi MinCoin** sau khi đọc input; không dùng chung phần Trace đệ quy ở trên cho bản này:

```cpp
vector<vector<int>> table(m + 1, vector<int>(X + 1, INF));
for (int i = 0; i <= m; ++i) table[i][0] = 0;
for (int i = 1; i <= m; ++i) {
    for (int x = 1; x <= X; ++x) {
        table[i][x] = table[i - 1][x];
        if (x >= D[i] && table[i][x - D[i]] != INF) {
            table[i][x] = min(table[i][x], table[i][x - D[i]] + 1);
        }
    }
}
if (table[m][X] == INF) cout << -1;
else cout << table[m][X];
```

Bản vòng lặp vẫn giữ nguyên trạng thái hai chiều của slide, không nén bảng. Nếu bảng quá lớn không cấp phát được thì cần cách khác theo giới hạn đề. Mỗi đồng chỉ dùng một lần là biến thể khác, không dùng nguyên mẫu vô hạn.

### 15B. Hệ 1,5,10,25,100 trong slide

```cpp
ll x;cin>>x;ll ans=0;
for(ll coin:{100LL,25LL,10LL,5LL,1LL}){ans+=x/coin;x%=coin;}
cout<<ans;
```

Không làm vậy cho mọi hệ: `{1,5,7}`, X=10, lấy 7 trước dùng 4 đồng nhưng 5+5 chỉ 2.

## 16. Cái túi: mỗi món được lấy thế nào?

### 16A. Mỗi món tối đa một lần, cần tối ưu chính xác

DP sau là phần thực hành bổ sung liên hệ bài cái túi C3. Input n B rồi n cặp weight value; trọng lượng dương, giá trị không âm, được chọn rỗng, B vừa để tạo mảng:

```cpp
int n,B;cin>>n>>B;vector<ll>dp(B+1,0);
for(int i=0;i<n;++i){
    int w;ll val;cin>>w>>val;
    for(int cap=B;cap>=w;--cap)dp[cap]=max(dp[cap],dp[cap-w]+val);
}
cout<<dp[B];
```

B=11, `(4,9),(5,10),(6,18),(10,27)` →**28** (món 2+3). Cap đi **xuống** để không lấy lại món. Nếu vô hạn, cap đi **lên**. Nếu phải đúng cân nặng B, khởi tạo dp[0]=0, còn lại -INF và chỉ chuyển từ ô có nghiệm.

Cần in món: lưu bảng dp[i][cap], truy ngược: nếu lấy i thì trừ w[i], rồi giảm i. Không gắn parent đơn giản vào bảng 1D rồi tin rằng luôn truy đúng món 0/1.

### 16B. Đề yêu cầu các cách tham lam trong slide

Ba cách: giá trị giảm; trọng lượng tăng; tỷ lệ giá trị/trọng lượng giảm. Cách 4 lấy tốt nhất trong ba kết quả. Dùng hai mảng w/value và sort **chỉ số** đồ vật, không cần kiểu dữ liệu tự định nghĩa. Biến và hàm trước main:

```cpp
int n;
vector<ll> w, value;

bool byValue(int i, int j) {
    return value[i] > value[j];
}

bool byWeight(int i, int j) {
    return w[i] < w[j];
}

bool byRatio(int i, int j) {
    return (long double)value[i] / w[i] > (long double)value[j] / w[j];
}

ll greedyBag(ll capacity, int mode) {
    vector<int> order(n);
    for (int i = 0; i < n; ++i) order[i] = i;
    if (mode == 1) sort(order.begin(), order.end(), byValue);
    else if (mode == 2) sort(order.begin(), order.end(), byWeight);
    else sort(order.begin(), order.end(), byRatio);

    ll ans = 0;
    for (int i : order) {
        if (w[i] <= capacity) {
            capacity -= w[i];
            ans += value[i];
        }
    }
    return ans;
}
```

Trong main, input n B rồi n cặp trọng lượng/giá trị; minh họa in kết quả cách 4:

```cpp
ll B;
cin >> n >> B;
w.resize(n);
value.resize(n);
for (int i = 0; i < n; ++i) cin >> w[i] >> value[i];
cout << max({greedyBag(B, 1), greedyBag(B, 2), greedyBag(B, 3)});
```

Ví dụ 16A: ba cách cho 27,19,27; cách 4 vẫn là 27, **không bằng tối ưu 28**. Không dùng thay DP nếu đề đòi tối ưu chính xác. Mọi w>0; tỷ lệ rất sát nhau có thể cần so tích chéo bằng kiểu đủ rộng.

### 16C. Cho phép cắt lẻ

Dùng lại biến n,w,value và hàm byRatio ở 16B; thêm hàm sau trước main:

```cpp
long double fractionalBag(ll capacity) {
    vector<int> order(n);
    for (int i = 0; i < n; ++i) order[i] = i;
    sort(order.begin(), order.end(), byRatio);
    long double ans = 0;
    for (int i : order) {
        ll take = min(capacity, w[i]);
        ans += (long double)take * value[i] / w[i];
        capacity -= take;
        if (capacity == 0) break;
    }
    return ans;
}
```

Main đọc như 16B, thay dòng cuối bằng `cout << fixed << setprecision(6) << fractionalBag(B);`, sửa số chữ số theo đề. Một món w=10,value=20, B=5 →10. Không cắt lẻ nếu đề không cho phép.

## 17. Sắp xếp / yêu cầu chia để trị

Bình thường dùng `sort(a.begin(),a.end());`. Nếu yêu cầu tự cài merge sort, đặt hàm trước main:

```cpp
void mergeSort(vector<ll>&a,int l,int r){
    if(l>=r)return;
    int m=(l+r)/2;mergeSort(a,l,m);mergeSort(a,m+1,r);
    vector<ll>t;int i=l,j=m+1;
    while(i<=m && j<=r){
        if(a[i]<=a[j])t.push_back(a[i++]);else t.push_back(a[j++]);
    }
    while(i<=m)t.push_back(a[i++]);while(j<=r)t.push_back(a[j++]);
    for(int k=0;k<(int)t.size();++k)a[l+k]=t[k];
}
```

Gọi `mergeSort(a,0,(int)a.size()-1);`. Cần giữ ID gốc thì sort cặp (giá trị, chỉ số). Trong thi code không cần chép định lý Master.

## 18. Đoạn liên tiếp tổng lớn nhất: theo hai cách trong slide

Input n≥1 và n số, đoạn không rỗng. `-2 3 -1 5 -6` →**7**, đoạn 2..4. `-5 -2 -7` →**-2**, không phải 0.

### 18A. Chương 5: đệ quy có nhớ

**MaxSum(i)** là tổng lớn nhất của đoạn **kết thúc tại i**. Hoặc bắt đầu mới ở i, hoặc nối a[i] vào đoạn kết thúc ở i-1. Giữ nguyên mảng mem cho dễ nhìn và truy vết.

Biến và hàm trước main, chỉ số từ 1:

```cpp
int n;
vector<ll> a, mem;
vector<bool> computed;

ll MaxSum(int i) {
    if (computed[i]) return mem[i];

    if (i == 1) mem[i] = a[i];
    else mem[i] = max(a[i], a[i] + MaxSum(i - 1));

    computed[i] = true;
    return mem[i];
}

void Trace(int i) {
    if (i > 1 && mem[i] == a[i] + mem[i - 1]) {
        Trace(i - 1);
    }
    cout << a[i] << ' ';
}
```

Main:

```cpp
cin >> n;
a.resize(n + 1);
mem.resize(n + 1);
computed.assign(n + 1, false);
for (int i = 1; i <= n; ++i) cin >> a[i];

ll answer = a[1];
int pos = 1;
for (int i = 1; i <= n; ++i) {
    ll value = MaxSum(i);
    if (value > answer) {
        answer = value;
        pos = i;
    }
}
cout << answer;
// Neu doi in cac phan tu cua doan:
// cout << '\n';
// Trace(pos);
```

Dùng computed riêng vì kết quả -1 vẫn có thể hợp lệ. Quét i tăng để tính đủ mem và không tạo chuỗi đệ quy dài ngay từ lần gọi đầu. Với đoạn rất dài, truy vết bằng vòng lặp thay Trace để tránh đệ quy sâu. Nếu chỉ cần giá trị thì không gọi Trace.

### 18B. Chương 4: chia để trị

Dùng hàm riêng dưới đây khi muốn theo chương 4. Mảng chỉ số từ **0**, gọi `maxSub(a,0,n-1)`:

```cpp
ll maxSub(const vector<ll>& a, int l, int r) {
    if (l == r) return a[l];
    int mid = (l + r) / 2;
    ll left = maxSub(a, l, mid);
    ll right = maxSub(a, mid + 1, r);

    ll sum = 0;
    ll suffix = LLONG_MIN;
    for (int i = mid; i >= l; --i) {
        sum += a[i];
        suffix = max(suffix, sum);
    }

    sum = 0;
    ll prefix = LLONG_MIN;
    for (int i = mid + 1; i <= r; ++i) {
        sum += a[i];
        prefix = max(prefix, sum);
    }
    return max(max(left, right), suffix + prefix);
}
```

Không trộn chỉ số từ 1 của bản 18A vào lời gọi bản 18B. Nếu đề cho đoạn rỗng thì đáp án có thể lấy thêm max với 0; nếu có quy tắc phá hòa vị trí/độ dài thì cần xử lý đúng yêu cầu đó.

## 19. Ngưỡng nhỏ nhất làm được: chặt nhị phân đáp án

Nhận dạng: thử M, kiểm tra được có làm được không; M tăng thì từ không làm được chuyển sang làm được và không quay lại.

**Ví dụ thực hành bổ sung:** chia dãy không âm thành tối đa K đoạn, nhỏ nhất tổng đoạn lớn nhất. `7 2 5 10 8`, K=2 →**18**: `[7,2,5]` và `[10,8]`. Biến và hàm trước main:

```cpp
int n, K;
vector<ll> a;

bool check(ll limit) {
    int groups = 1;
    ll sum = 0;
    for (int i = 0; i < n; ++i) {
        if (a[i] > limit) return false;
        if (sum > limit - a[i]) {
            ++groups;
            sum = a[i];
        } else {
            sum += a[i];
        }
    }
    return groups <= K;
}

ll binarySearch(ll lo, ll hi) {
    if (!check(hi)) return -1;
    while (lo < hi) {
        ll mid = lo + (hi - lo) / 2;
        if (check(mid)) hi = mid;
        else lo = mid + 1;
    }
    return lo;
}
```

Main: n K, n số, n≥1,K≥1:

```cpp
cin >> n >> K;
a.resize(n);
ll lo = 0, hi = 0;
for (int i = 0; i < n; ++i) {
    cin >> a[i];
    lo = max(lo, a[i]);
    hi += a[i];
}
cout << binarySearch(lo, hi);
```

Gặp bài khác: thay nội dung check và miền lo/hi theo đề. Tìm **lớn nhất làm được**, giá trị lớn làm bài khó hơn: mid lệch phải `lo+(hi-lo+1)/2`; đúng thì lo=mid, sai thì hi=mid-1. Kiểm tra có nghiệm trước.

Số thực, ví dụ sqrt(x), đoạn sau đặt trong main riêng:

```cpp
long double x;
cin >> x; // x >= 0
long double lo = 0, hi = max(1.0L, x);
for (int rep = 0; rep < 100; ++rep) {
    long double mid = (lo + hi) / 2;
    if (mid * mid >= x) hi = mid;
    else lo = mid;
}
cout << fixed << setprecision(6) << hi;
```

Miền quá lớn/sai số khác cần điều chỉnh số vòng. Tìm nghiệm hàm bằng chia đôi phải có điều kiện phù hợp, chẳng hạn liên tục và hai đầu trái dấu. Tìm phần tử trong mảng sort dùng mục 4.

## 20. a^n mod m: đệ quy theo cách chia để trị trong slide

**Gặp bài:** tính a^n mod m. Tách rõ ba trường hợp: n=0; n lẻ; n chẵn. Với n chẵn, chỉ tính nửa số mũ một lần rồi bình phương.

Hàm đặt trước main; n≥0, mod≥1. Mẫu nhân trực tiếp khi tích hai số dư vừa long long, ví dụ mod≤10^9+7.

```cpp
ll power(ll a, ll n, ll mod) {
    if (n == 0) {
        return 1 % mod;
    }

    if (n % 2 == 1) {
        return (a * power(a, n - 1, mod)) % mod;
    }

    ll half = power(a, n / 2, mod);
    return (half * half) % mod;
}
```

Trong main:

```cpp
ll a, n, mod;
cin >> a >> n >> mod;
a = a % mod;
if (a < 0) a = a + mod;
cout << power(a, n, mod);
```

Ví dụ `3 13 100` →**23**. Không dùng pow số thực để tính số nguyên lớn chính xác. Mod rất lớn cần phép nhân chống tràn; mẫu trên chưa xử lý trường hợp đó. Không mod thì bỏ phép lấy dư chỉ khi biết kết quả và các phép nhân vừa kiểu số.

## 21. Fibonacci số và xâu

### 21A. Tính số Fibonacci bằng đệ quy có nhớ

**Theo chương 5:** F1=F2=1, Fn=F[n-1]+F[n-2]. Mảng mem lưu số đã tính; -1 nghĩa là chưa tính. Không dùng mẹo giữ hai biến trong mẫu chính.

Biến và hàm trước main, **1≤n≤92** để kết quả vừa long long:

```cpp
ll mem[93];

ll Fib(int n) {
    if (n == 1 || n == 2) {
        return 1;
    }

    if (mem[n] != -1) {
        return mem[n];
    }

    mem[n] = Fib(n - 1) + Fib(n - 2);
    return mem[n];
}
```

Trong main:

```cpp
int n;
cin >> n;
for (int i = 0; i <= 92; ++i) mem[i] = -1;
cout << Fib(n);
```

Ví dụ n=6 →8. Nếu đề dùng F0=0 thì thêm trường hợp n=0 trả 0. n>92 cần số lớn hoặc tính modulo theo đề, không chỉ tăng kích thước mảng. Điều kiện lấy kết quả đã nhớ là **mem[n] != -1**; một số dòng trên slide bị viết ngược dấu này.

### 21B. Ký tự thứ k của xâu Fibonacci: chia thành xâu trái và xâu phải

**Theo slide:** G1=A, G2=B, Gn=G[n-2]+G[n-1].

| n | Xâu Gn | Độ dài |
|---|---|---:|
| 1 | A | 1 |
| 2 | B | 1 |
| 3 | AB | 2 |
| 4 | BAB | 3 |
| 5 | ABBAB | 5 |
| 6 | BABABBAB | 8 |

**Gặp bài tìm ký tự thứ k, làm như sau:**

1. Tính mảng độ dài: `len[1]=len[2]=1`, `len[i]=len[i-2]+len[i-1]`.
2. Gn gồm **phần trái G[n-2]** và **phần phải G[n-1]**.
3. Nếu `k <= len[n-2]`: ký tự nằm bên trái, tìm tiếp trong G[n-2], giữ nguyên k.
4. Nếu k lớn hơn: ký tự nằm bên phải, bỏ qua độ dài bên trái bằng `k-len[n-2]`, tìm trong G[n-1].
5. Đến G1 thì trả A, đến G2 thì trả B.

Slide nêu cách chia để trị, không cung cấp nguyên một chương trình cho bài này. Code dưới diễn đạt trực tiếp ý tưởng đó, không thêm mẹo chặn độ dài. **Mẫu dùng cho 1≤n≤92, 1≤k≤len[n]**; chỉ số ký tự bắt đầu từ 1.

Chương trình đầy đủ:

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

ll len[93];

void buildLength(int n) {
    len[1] = 1;
    len[2] = 1;
    for (int i = 3; i <= n; ++i) {
        len[i] = len[i - 2] + len[i - 1];
    }
}

char findChar(int n, ll k) {
    if (n == 1) {
        return 'A';
    }
    if (n == 2) {
        return 'B';
    }

    // Phan trai la G[n-2].
    if (k <= len[n - 2]) {
        return findChar(n - 2, k);
    }

    // Phan phai la G[n-1]: tru so ky tu cua phan trai.
    return findChar(n - 1, k - len[n - 2]);
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    ll k;
    cin >> n >> k;
    buildLength(n);
    cout << findChar(n, k);
    return 0;
}
```

**Chạy tay n=6,k=5:** G6 = G4 + G5, G4 dài 3 nên chuyển sang G5, vị trí 5-3=2. G5 = G3 + G4, G3 dài 2 nên chuyển sang G3, vị trí 2. G3 = G1 + G2, G1 dài 1 nên chuyển sang G2, vị trí 1. G2 là B → in **B**.

Nếu đề n>92, độ dài vượt long long: mẫu đơn giản này chưa dùng được, cần bổ sung cách lưu độ dài phù hợp. Nếu đề ghép G[n-1]+G[n-2] thì phải đổi thứ tự hai nhánh; không chép nguyên quy ước này.

## 22. Dãy con tăng dài nhất: LIS theo slide

Được bỏ phần tử nhưng giữ thứ tự, không cần liên tiếp. `2 0 6 1 2 9` →`0 1 2 9`, dài **4**.

**LIS(i)** là độ dài dãy con tăng **kết thúc tại phần tử i**. Bắt đầu lấy riêng a[i] thì dài 1. Thử các j<i có a[j]<a[i], nối a[i] vào sau dãy kết thúc j. Lưu kết quả vào mem để không tính lại.

Biến và hàm trước main, dùng chỉ số từ 1:

```cpp
int n;
vector<ll> a;
vector<int> mem;

int LIS(int i) {
    if (mem[i] != -1) {
        return mem[i];
    }

    int result = 1;
    for (int j = 1; j < i; ++j) {
        if (a[j] < a[i]) {
            result = max(result, LIS(j) + 1);
        }
    }

    mem[i] = result;
    return result;
}

void Trace(int i) {
    // Tim phan tu dung truoc i trong mot day tang tot nhat.
    for (int j = i - 1; j >= 1; --j) {
        if (a[j] < a[i] && mem[i] == mem[j] + 1) {
            Trace(j);
            break;
        }
    }
    cout << a[i] << ' ';
}
```

Trong main:

```cpp
cin >> n;
a.resize(n + 1);
mem.assign(n + 1, -1);
for (int i = 1; i <= n; ++i) cin >> a[i];

int answer = 0;
int pos = 0;
for (int i = 1; i <= n; ++i) {
    int length = LIS(i);
    if (length > answer) {
        answer = length;
        pos = i;
    }
}
cout << answer << '\n';
// Chi them hai dong sau neu de yeu cau in day:
// if (pos != 0) Trace(pos);
// cout << '\n';
```

Ví dụ trên mem[1..6]=`1 1 2 2 3 4`. Đáp án là lớn nhất trong các LIS(i), **không chỉ LIS(n)**. Tăng chặt dùng <; không giảm dùng <= ở cả LIS và Trace. `2 2 2` tăng chặt dài 1. Muốn in chỉ số thì trong Trace in i thay cho a[i].

Đây là cách chính trong slide. Với n hàng trăm nghìn, bản này không phù hợp; khi đề có giới hạn đó mới cần bản cải tiến. Bản tail/lower_bound không đặt làm mẫu học chính nữa.

## 23. Dãy con chung dài nhất: LCS theo slide

X=`abcb`, Y=`bdcab` →dài **3**, một đáp án `bcb`.

**LCS(i,j)** là độ dài dãy con chung của i ký tự đầu X và j ký tự đầu Y. Thử bỏ ký tự cuối một bên; nếu hai ký tự cuối giống nhau thì có thêm cách lấy cặp đó và giải hai phần đầu ngắn hơn.

Biến và hàm trước main:

```cpp
string X, Y;
vector<vector<int>> mem;

int LCS(int i, int j) {
    if (i == 0 || j == 0) {
        return mem[i][j] = 0;
    }
    if (mem[i][j] != -1) {
        return mem[i][j];
    }

    int result = max(LCS(i - 1, j), LCS(i, j - 1));
    if (X[i - 1] == Y[j - 1]) {
        result = max(result, LCS(i - 1, j - 1) + 1);
    }

    mem[i][j] = result;
    return result;
}

void Trace(int i, int j) {
    if (i == 0 || j == 0) return;

    if (X[i - 1] == Y[j - 1]) {
        Trace(i - 1, j - 1);
        cout << X[i - 1];
    } else if (LCS(i - 1, j) >= LCS(i, j - 1)) {
        Trace(i - 1, j);
    } else {
        Trace(i, j - 1);
    }
}
```

Trong main, input hai xâu không khoảng trắng:

```cpp
cin >> X >> Y;
int n = X.size();
int m = Y.size();
mem.assign(n + 1, vector<int>(m + 1, -1));
cout << LCS(n, m) << '\n';
// Chi them neu de yeu cau in xau:
// Trace(n, m);
// cout << '\n';
```

Có dòng rỗng/khoảng trắng thì đọc getline theo đề. Nếu đề hỏi **đoạn chung liên tiếp**, không dùng nguyên công thức LCS. Nếu yêu cầu đáp án nhỏ nhất từ điển, cách phá hòa này chưa đảm bảo. Xâu rất dài có thể vượt bộ nhớ/độ sâu đệ quy: cần xem giới hạn đề trước khi dùng mẫu bài giảng.

## 24. Đi qua mọi thành phố rồi về đầu: TSP bitmask

n đỉnh từ 0, xuất phát 0, mỗi đỉnh đúng một lần rồi về 0. Ma trận đầy đủ, chi phí không âm, tổng tour nhỏ hơn INF.

- Đã thăm j: `mask & (1 << j)`.
- Thêm j: `mask | (1 << j)`.
- Ban đầu chỉ có 0: mask=1.
- Đã đủ: mask=(1<<n)-1.

Biến và hàm trước main:

```cpp
const ll INF = LLONG_MAX / 4;
int n, full;
vector<vector<ll>> c, dp;

ll tsp(int u, int mask) {
    if (mask == full) return c[u][0];
    if (dp[u][mask] != -1) return dp[u][mask];
    ll ans = INF;
    for (int v = 1; v < n; ++v) {
        if (mask & (1 << v)) continue;
        ans = min(ans, c[u][v] + tsp(v, mask | (1 << v)));
    }
    dp[u][mask] = ans;
    return ans;
}

void printRoute() {
    int u = 0, mask = 1;
    cout << 0;
    while (mask != full) {
        for (int v = 1; v < n; ++v) {
            if (mask & (1 << v)) continue;
            if (tsp(u, mask) == c[u][v] + tsp(v, mask | (1 << v))) {
                cout << ' ' << v;
                mask |= (1 << v);
                u = v;
                break;
            }
        }
    }
    cout << ' ' << 0 << '\n';
}
```

Main: n≥1 rồi ma trận n×n:

```cpp
cin >> n;
c.assign(n, vector<ll>(n));
for (int i = 0; i < n; ++i)
    for (int j = 0; j < n; ++j) cin >> c[i][j];
full = (1 << n) - 1;
dp.assign(n, vector<ll>(1 << n, -1));
cout << tsp(0, 1) << '\n';
// Neu de yeu cau in tuyen, them: printRoute();
```

Ma trận `0 10 15 20 / 10 0 35 25 / 15 35 0 30 / 20 25 30 0` →**80**, tour 0→1→3→2→0.

- Full mask phải cộng cạnh về 0. Vòng v<n, không v<=n.
- Memo đã tính: !=-1. Kết quả hợp lệ có thể âm thì dùng seen riêng.
- n=20, riêng bảng ll khoảng 160 MiB; kiểm tra bộ nhớ. Chỉ đổi sang int nếu chắc tổng tour vừa int. Không dùng cho n hàng trăm.
- 0/-1 nghĩa **không có cạnh**: phải bỏ các chuyển đó và xử lý vô nghiệm; mẫu không tự hiểu cạnh thiếu.
- Không dùng TSP nguyên mẫu cho xe có tải/đón-trả. n=1 thường có c[0][0]=0.

## 25. Bị lỗi khi submit thì kiểm tra gì?

| Kết quả | Kiểm tra ngay |
|---|---|
| Compile Error | Thiếu dấu ;, thiếu header, đặt hàm trong main, trùng tên, chọn C++17 |
| Wrong Answer | Input sai, chỉ số 0/1, in thừa, dấu < hoặc <=, tràn int, quên reset |
| Runtime Error | Chỉ số -1/n, top khi rỗng, chưa resize, đệ quy quá sâu |
| Time Limit | Duyệt quá nhiều, quên memo, nhị phân không tiến, hai vòng khi n quá lớn |
| Memory Limit | Bảng LCS/TSP quá lớn, cấp mảng không theo giới hạn đề |

Test nên tự tạo: một phần tử; tất cả bằng nhau; không nghiệm; đáp án đầu/cuối; tổng vượt 2 tỷ; dãy toàn âm; tải vừa đúng sức chứa; xe rỗng; số tiền 0; predicate toàn đúng/toàn sai.

## 26. Cách làm bài thi 4 bài code

1. Đọc cả 4 bài, chọn bài nhận dạng chắc nhất để có AC sớm.
2. Với mỗi bài ghi nhanh: input / output / mục mẫu / chỗ cần sửa.
3. Bí thì tạo test nhỏ, tính tay, so code. Đừng sửa dấu ngẫu nhiên.
4. Nếu hệ thống chấm từng nhóm test, có thể làm bản đúng cho nhóm nhỏ trước rồi nâng cấp; chỉ áp dụng khi quy chế thực sự hỗ trợ.
5. Dành thời gian cuối xem trạng thái nộp, bỏ debug, kiểm tra đúng file/đúng bài.

**Phạm vi:** giữ các dạng chính của 5 chương trong 6 PDF. Phần cắt 2D chỉ có tên trong nguồn nên khung được ghi rõ có điều kiện. Các ví dụ bổ sung phục vụ code đã được đánh dấu. Không thêm phần chứng minh, tính độ phức tạp hay câu hỏi tự luận. Các thuật toán đồ thị/hình học chỉ nhắc tên trong slide không được coi là bài giảng chi tiết ở đây.

