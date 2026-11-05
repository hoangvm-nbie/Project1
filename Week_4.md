# Bài 16: Quản lý tập hợp chuỗi - Tra cứu và Thêm phần tử (Set Operations)

## 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu

* **Kỹ thuật:** Sử dụng cấu trúc dữ liệu Tập hợp (`std::set<string>`) trong C++ STL, được cài đặt ngầm định bằng Cây đỏ-đen (Red-Black Tree - một dạng cây nhị phân tìm kiếm tự cân bằng).
* **Mục đích:**
  * Lưu trữ tập hợp các khóa `k` (chuỗi ký tự) sao cho không có phần tử nào trùng lặp và luôn được sắp xếp tự động.
  * Truy vấn `find k`: Kiểm tra xem khóa `k` đã tồn tại trong tập hợp hay chưa (trả về `1` nếu có, `0` nếu không).
  * Truy vấn `insert k`: Thêm khóa `k` vào tập hợp (trả về `1` nếu thêm thành công do `k` chưa tồn tại, trả về `0` nếu thêm thất bại do `k` đã có sẵn).
* **Logic hoạt động:**
  * Đọc danh sách các khóa ban đầu cho đến khi gặp dấu `*` và đưa vào tập hợp bằng lệnh `s.insert(k)`. Các khóa trùng nhau sẽ tự động bị loại bỏ.
  * Với truy vấn `find`: Dùng hàm `s.count(k)`. Vì `std::set` chỉ chứa các phần tử duy nhất (unique), hàm `count(k)` chỉ trả về `1` (tồn tại) hoặc `0` (không tồn tại) — khớp hoàn toàn với yêu cầu đầu ra của bài toán.
  * Với truy vấn `insert`: Hàm `s.insert(k)` trả về một cặp giá trị `std::pair<iterator, bool>`. Thành phần `.second` mang kiểu `bool` cho biết việc chèn có thành công (tạo ra nút mới) hay không.

## 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp

* **Thầy cô hỏi:** *"Tại sao ở lệnh `insert` lại viết `if (s.insert(k).second)`? Giá trị trả về của hàm `s.insert(k)` thực chất là gì?"*
  * **Trả lời:** Hàm `s.insert(k)` trong `std::set` trả về một `std::pair<iterator, bool>`. Trong đó:
    * `first`: Là một iterator trỏ tới phần tử `k` trong set (dù là vừa mới chèn vào hay đã có sẵn từ trước).
    * `second`: Là giá trị `bool` — trả về `true` nếu chèn thành công (trước đó `k` chưa có trong set) và `false` nếu chèn thất bại (do `k` đã tồn tại).
    * Việc gọi thẳng `s.insert(k).second` giúp vừa thực hiện thao tác chèn, vừa kiểm tra được kết quả chỉ trong **1 lần duyệt cây**, thay vì phải gọi `s.find(k)` kiểm tra trước rồi mới `s.insert(k)` (tốn 2 lần duyệt cây).
* **Thầy cô hỏi:** *"Tại sao ở truy vấn `find` lại dùng `s.count(k)` mà không dùng `s.find(k) != s.end()`?"*
  * **Trả lời:** Cả hai cách đều có cùng độ phức tạp $O(L \log N)$ trên `std::set`. Tuy nhiên, do đặc tính của `set` là mỗi phần tử chỉ xuất hiện tối đa 1 lần, nên `s.count(k)` chỉ trả về `0` hoặc `1`. Dùng `s.count(k)` giúp in trực tiếp kết quả ra màn hình ngắn gọn hơn mà không cần viết thêm câu lệnh điều kiện `if-else`.
* **Thầy cô hỏi:** *"Độ phức tạp thời gian của mỗi thao tác `find` và `insert` là bao nhiêu? Có cách nào tối ưu nhanh hơn nữa không?"*
  * **Trả lời:** Với $N$ phần tử trong `set` và độ dài chuỗi tối đa là $L$, mỗi thao tác tốn $O(L \log N)$ do phải đi qua $\log N$ tầng của cây nhị phân cân bằng và mỗi tầng mất $O(L)$ để so sánh chuỗi. Nếu bài toán không yêu cầu duy trì thứ tự từ điển, ta có thể thay `std::set` bằng `std::unordered_set` (bảng băm - Hash Table) để giảm độ phức tạp trung bình xuống $O(L)$ cho mỗi truy vấn.

## 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    // Tối ưu tốc độ nhập xuất (I/O)
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    set<string> s;
    string k, cmd;

    // Đọc các phần tử ban đầu của tập hợp cho đến khi gặp "*"
    while (cin >> k && k != "*")
        s.insert(k);

    // Xử lý các truy vấn cho đến khi gặp "***"
    while (cin >> cmd && cmd != "***") {
        cin >> k;

        if (cmd == "find") {
            // s.count(k) trả về 1 nếu có, 0 nếu không tồn tại
            cout << s.count(k) << '\n';
        }
        else if (cmd == "insert") {
            // .second trả về true nếu chèn mới thành công, false nếu đã tồn tại
            if (s.insert(k).second)
                cout << 1 << '\n';
            else
                cout << 0 << '\n';
        }
    }

    return 0;
}
```
# Bài 17: Đếm số cặp có tổng bằng M (Two Sum / Two Pointers)

## 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu

* **Kỹ thuật:** Sắp xếp (`std::sort`) kết hợp kỹ thuật Hai con trỏ (Two Pointers) hội tụ từ hai đầu mảng (`std::vector<int>`).
* **Mục đích:**
  * Đếm số cặp chỉ số $(i, j)$ sao cho $1 \le i < j \le n$ và $a_i + a_j = M$ với điều kiện các phần tử trong mảng **đôi một khác nhau**.
  * Giảm độ phức tạp từ $O(N^2)$ (duyệt trâu 2 vòng lặp lồng nhau) xuống $O(N \log N)$ để chạy mượt mà với giới hạn $N \le 1,000,000$.
* **Logic hoạt động:**
  * **Bước 1 (Sắp xếp):** Sắp xếp mảng $a$ theo thứ tự tăng dần. Phép cộng có tính chất giao hoán nên việc đổi chỗ các phần tử không làm thay đổi số lượng cặp có tổng bằng $M$.
  * **Bước 2 (Đặt 2 con trỏ):** Đặt con trỏ trái `l = 0` (phần tử nhỏ nhất) và con trỏ phải `r = n - 1` (phần tử lớn nhất).
  * **Bước 3 (Dịch chuyển con trỏ):** Khi `l < r`, xét tổng `sum = a[l] + a[r]`:
    * Nếu `sum == M`: Tìm được 1 cặp thỏa mãn $\rightarrow$ tăng biến đếm `cnt++`, đồng thời dịch cả 2 con trỏ vào trong (`l++`, `r--`).
    * Nếu `sum < M`: Tổng hiện tại đang thiếu $\rightarrow$ cần tăng tổng lên bằng cách tăng phần tử nhỏ hơn (`l++`).
    * Nếu `sum > M`: Tổng hiện tại đang thừa $\rightarrow$ cần giảm tổng xuống bằng cách giảm phần tử lớn hơn (`r--`).

## 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp

* **Thầy cô hỏi:** *"Đề bài yêu cầu đếm cặp chỉ số $1 \le i < j \le n$ trên mảng ban đầu. Tại sao em tự ý `sort` làm xáo trộn hết vị trí của các phần tử mà kết quả vẫn đúng?"*
  * **Trả lời:** Vì phép cộng có tính chất giao hoán ($a_i + a_j = a_j + a_i$). Bất kỳ một tập hợp gồm 2 phần tử khác vị trí nào trong mảng đều tạo thành duy nhất 1 cặp $(i, j)$ thỏa mãn $i < j$ ở mảng gốc. Do đó, việc sắp xếp lại mảng chỉ thay đổi thứ tự đứng của các số chứ không làm thay đổi số lượng các cặp hai phần tử có tổng bằng $M$.
* **Thầy cô hỏi:** *"Khi tìm thấy `sum == M`, tại sao em được phép dịch chuyển đồng thời cả hai con trỏ (`l++` và `r--`) mà không sợ bỏ sót cặp nào khác kết hợp với `a[l]` hoặc `a[r]`?"*
  * **Trả lời:** Vì đề bài cho giả thiết cực kỳ quan trọng là **các phần tử đôi một khác nhau**. Khi đã có $a_l + a_r = M$, không thể tồn tại phần tử $a_k$ nào khác mà $a_l + a_k = M$ (vì điều đó suy ra $a_k = a_r$, vô lý do các số khác nhau đôi một). Do đó, cả $a_l$ và $a_r$ đều đã tìm được "nửa kia" duy nhất của mình và không thể ghép thêm với bất kỳ số nào khác nữa.
  * *(Lưu ý mở rộng: Nếu đề bài cho phép các phần tử trùng nhau, việc tăng đồng thời `l++`, `r--` như trên sẽ bị **sai/thiếu nghiệm**, khi đó phải đếm số lượng phần tử bằng `a[l]` và bằng `a[r]` rồi nhân lại).*
* **Thầy cô hỏi:** *"Tại sao điều kiện dừng vòng lặp `while` phải là `l < r` mà không phải `l <= r`?"*
  * **Trả lời:** Đề bài yêu cầu $i < j$, tức là hai phần tử được chọn phải nằm ở hai vị trí khác nhau. Nếu để `l <= r`, khi `l == r` thuật toán sẽ lấy một phần tử cộng với chính nó ($a_l + a_l = M$), vi phạm điều kiện $i < j$.
* **Thầy cô hỏi:** *"Độ phức tạp thời gian và không gian của thuật toán này là bao nhiêu?"*
  * **Trả lời:** 
    * Thời gian: Khâu sắp xếp `std::sort` tốn $O(N \log N)$. Vòng lặp `while` hai con trỏ mỗi bước đều thu hẹp khoảng cách `r - l` đi ít nhất 1 đơn vị nên chạy tối đa $N$ bước, tốn $O(N)$. Tổng độ phức tạp thời gian là $O(N \log N)$.
    * Không gian (Bộ nhớ): $O(N)$ để lưu trữ `vector<int> a` gồm $N$ phần tử.

## 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    // Tối ưu tốc độ nhập xuất (I/O) cho dữ liệu lớn (N <= 1,000,000)
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, M;
    cin >> n >> M;

    vector<int> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    // Bước 1: Sắp xếp mảng tăng dần - O(N log N)
    sort(a.begin(), a.end());

    // Bước 2: Dùng kỹ thuật 2 con trỏ ở 2 đầu mảng - O(N)
    int l = 0, r = n - 1;
    int cnt = 0;

    while (l < r) {
        int sum = a[l] + a[r];
        if (sum == M) {
            cnt++; // Tìm thấy 1 cặp thỏa mãn
            l++;   // Dịch cả 2 con trỏ vì các phần tử đôi một khác nhau
            r--;
        } else if (sum < M) {
            l++;   // Tổng nhỏ hơn M -> tăng con trỏ trái để tăng tổng
        } else {
            r--;   // Tổng lớn hơn M -> giảm con trỏ phải để giảm tổng
        }
    }

    cout << cnt << '\n';

    return 0;
}
```
# Bài 18: Kiểm tra Đồ thị Hamilton (Hamiltonian Cycle / Backtracking)

## 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu

* **Kỹ thuật:** Quay lui (Backtracking) kết hợp biểu diễn đồ thị bằng Ma trận kề (`int a[1000][1000]`) và mảng đánh dấu (`bool visited[1000]`).
* **Mục đích:**
  * Kiểm tra xem một đồ thị vô hướng $G = (V, E)$ có phải là **đồ thị Hamilton** hay không (tức là tồn tại một **chu trình Hamilton** đi qua tất cả $n$ đỉnh, mỗi đỉnh đúng 1 lần rồi quay trở về đỉnh xuất phát).
  * In ra `1` nếu tồn tại chu trình Hamilton, ngược lại in ra `0`.
* **Logic hoạt động:**
  * **Biểu diễn dữ liệu:**
    * `a[u][v] = 1` nếu có cạnh nối giữa đỉnh `u` và đỉnh `v` (vì đồ thị vô hướng nên `a[u][v] = a[v][u] = 1`).
    * Mảng `x[0...n-1]` lưu thứ tự các đỉnh trên đường đi.
    * Mảng `visited[v]` đánh dấu đỉnh `v` đã được đi qua hay chưa.
  * **Cố định đỉnh xuất phát:** Vì chu trình là một vòng khép kín đi qua mọi đỉnh, ta có thể cố định đỉnh đầu tiên luôn là đỉnh `1` (`x[0] = 1`, `visited[1] = true`) mà không làm mất tính tổng quát.
  * **Hàm đệ quy `Try(k)`:** Tìm đỉnh cho vị trí thứ `k` trong hành trình (với `k` chạy từ `1` đến `n - 1`):
    * Duyệt qua các đỉnh ứng cử viên `v` từ `2` đến `n`.
    * Nếu đỉnh `v` chưa thăm (`!visited[v]`) và có cạnh nối từ đỉnh ngay trước đó `x[k-1]` tới `v` (`a[x[k-1]][v] == 1`), ta chọn `x[k] = v`, đánh dấu `visited[v] = true`, rồi gọi đệ quy `Try(k + 1)`.
    * Sau khi nhánh đệ quy quay lui về, ta trả lại trạng thái `visited[v] = false` để thử các nhánh khác.
    * **Điều kiện dừng (`k == n`):** Khi đã đi qua đủ $n$ đỉnh (từ `x[0]` đến `x[n-1]`), ta kiểm tra xem đỉnh cuối cùng `x[n-1]` có cạnh nối trực tiếp về đỉnh xuất phát `x[0] = 1` hay không (`a[x[n-1]][1] == 1`). Nếu có, bật cờ `found = true`.

## 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp

* **Thầy cô hỏi:** *"Tại sao ta lại gán cứng `x[0] = 1` và vòng lặp `for` trong hàm `Try` chỉ chạy từ `v = 2` đến `n`? Nhỡ chu trình Hamilton bắt đầu từ đỉnh 2 hay đỉnh 3 thì sao?"*
  * **Trả lời:** Chu trình Hamilton là một **vòng tròn khép kín** đi qua **tất cả** $n$ đỉnh của đồ thị. Do đó, nếu tồn tại chu trình thì đỉnh `1` chắc chắn phải nằm trên chu trình đó. Việc chọn đỉnh `1` làm điểm bắt đầu chỉ là chọn mốc xuất phát trên vòng tròn, giúp giảm độ phức tạp đi $n$ lần (từ $O(n!)$ xuống $O((n-1)!)$) vì không phải thử lại các hoán vị xoay vòng tương đương (ví dụ: `1-2-3-1` và `2-3-1-2` là cùng một chu trình).
* **Thầy cô hỏi:** *"Dòng lệnh `if (found) return;` ở đầu hàm `Try` và dòng `visited[v] = false;` có tác dụng gì?"*
  * **Trả lời:**
    * `if (found) return;` là kỹ thuật **cắt tỉa sớm (Early Stopping)**. Bài toán chỉ hỏi "có tồn tại hay không" (in `1` hoặc `0`), nên ngay khi tìm được 1 chu trình Hamilton đầu tiên (`found = true`), ta thoát toàn bộ các lời gọi đệ quy đang chờ mà không cần duyệt thêm.
    * `visited[v] = false;` là bước **hoàn trả trạng thái (Backtrack)** kinh điển. Khi đi từ `x[k-1]` sang `v` mà dẫn tới ngõ cụt ở các bước sau, ta phải gỡ đánh dấu đỉnh `v` để các nhánh tìm kiếm khác có thể đi qua `v` theo một thứ tự khác.
* **Thầy cô hỏi:** *"Bài toán có nhiều bộ test `T`. Nếu quên hai dòng `memset` ở đầu mỗi vòng lặp `while(T--)` thì chuyện gì sẽ xảy ra?"*
  * **Trả lời:** Nếu quên `memset(a, 0, sizeof(a))` và `memset(visited, false, sizeof(visited))`, dữ liệu các cạnh của đồ thị ở test trước sẽ bị lưu đọng lại và gộp chung vào test sau, dẫn đến kết quả kiểm tra có cạnh nối bị sai hoàn toàn.
* **Thầy cô hỏi:** *"Độ phức tạp thời gian trong trường hợp xấu nhất của thuật toán này là bao nhiêu?"*
  * **Trả lời:** Bài toán tìm chu trình Hamilton là bài toán **NP-Complete**. Trong trường hợp xấu nhất (đồ thị gần đầy đủ nhưng không có chu trình Hamilton), thuật toán quay lui phải thử hầu hết các hoán vị của $n-1$ đỉnh còn lại, nên độ phức tạp thời gian là $O((n-1)!)$.

## 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, m;
int a[1000][1000], x[1000];
bool visited[1000];
bool found;

// Hàm quay lui tìm đỉnh thứ k trong chu trình (k từ 1 đến n-1)
void Try(int k) {
    if (found) return; // Cắt tỉa: Đã tìm thấy ít nhất 1 chu trình thì dừng ngay
    
    // Đã chọn đủ n đỉnh (từ x[0] đến x[n-1])
    if (k == n) {
        // Kiểm tra xem đỉnh cuối cùng có cạnh nối về đỉnh xuất phát (đỉnh 1) không
        if (a[x[n - 1]][1])
            found = true;
        return;
    }
    
    // Thử chọn đỉnh tiếp theo v (từ 2 đến n)
    for (int v = 2; v <= n; v++) {
        // Nếu v chưa thăm và có cạnh nối từ đỉnh trước đó x[k-1] tới v
        if (!visited[v] && a[x[k - 1]][v]) {
            x[k] = v;
            visited[v] = true;
            
            Try(k + 1);
            
            visited[v] = false; // Quay lui (Backtrack): trả lại trạng thái
        }
    }
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int T;
    cin >> T;
    while (T--) {
        cin >> n >> m;
        
        // Reset ma trận kề và mảng đánh dấu cho mỗi test case mới
        memset(a, 0, sizeof(a));
        memset(visited, false, sizeof(visited));
        
        for (int i = 0; i < m; i++) {
            int u, v;
            cin >> u >> v;
            a[u][v] = a[v][u] = 1; // Đồ thị vô hướng
        }
        
        found = false;
        x[0] = 1;          // Cố định đỉnh xuất phát là đỉnh 1
        visited[1] = true; // Đánh dấu đỉnh 1 đã được thăm
        
        Try(1);            // Bắt đầu tìm từ vị trí thứ 1 (đỉnh thứ 2 của chu trình)
        
        cout << found << '\n';
    }
    return 0;
}
```
# Bài 19: Cây khung nhỏ nhất - Thuật toán Kruskal (Minimum Spanning Tree / Disjoint Set Union)

## 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu

* **Kỹ thuật:** Thuật toán tham lam **Kruskal** kết hợp cấu trúc dữ liệu Các tập hợp rời nhau (**DSU - Disjoint Set Union** hay **Union-Find**).
* **Mục đích:**
  * Tìm **Cây khung nhỏ nhất (Minimum Spanning Tree - MST)** của đồ thị vô hướng liên thông $G=(V, E)$ gồm $N$ đỉnh và $M$ cạnh có trọng số.
  * Tính và in ra tổng trọng số của $N - 1$ cạnh tạo nên cây khung đó.
* **Logic hoạt động:**
  * **Bước 1 (Biểu diễn & Sắp xếp cạnh):** Lưu danh sách các cạnh dưới dạng `struct Edge { u, v, w }`. Sắp xếp toàn bộ $M$ cạnh theo thứ tự trọng số `w` tăng dần (Chiến lược tham lam: ưu tiên chọn cạnh rẻ nhất trước).
  * **Bước 2 (Khởi tạo DSU):** Ban đầu coi mỗi đỉnh là một thành phần liên thông độc lập: `parent[i] = i` (cha của `i` là chính nó) và `sz[i] = 1` (kích thước tập hợp bằng 1).
  * **Bước 3 (Duyệt cạnh & Hợp nhất):** Duyệt lần lượt các cạnh từ trọng số nhỏ nhất đến lớn nhất:
    * Với mỗi cạnh `(u, v, w)`, dùng hàm `Union(u, v)` để kiểm tra xem `u` và `v` đã thuộc cùng một thành phần liên thông hay chưa.
    * Nếu chưa cùng tập hợp (`find(u) != find(v)`): Việc thêm cạnh này **không tạo chu trình** $\rightarrow$ nối 2 tập hợp lại, cộng `w` vào tổng trọng số `ans` và tăng số cạnh đã chọn `cnt++`.
    * **Điều kiện dừng sớm:** Ngay khi chọn đủ `cnt == n - 1` cạnh (số cạnh tiêu chuẩn của một cây có $N$ đỉnh), dừng vòng lặp ngay lập tức.

## 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp

* **Thầy cô hỏi:** *"Cấu trúc DSU trong code của em đã áp dụng những kỹ thuật tối ưu nào? Nếu bỏ chúng đi thì điều gì xảy ra?"*
  * **Trả lời:** Trong code áp dụng 2 kỹ thuật tối ưu kinh điển của DSU:
    1. **Nén đường đi (Path Compression)** ở hàm `find`: Dòng `return parent[u] = find(parent[u]);` giúp gán thẳng mọi nút trên đường đi vào gốc đại diện, làm phẳng cây.
    2. **Gộp theo kích thước (Union by Size)** ở hàm `Union`: Dòng `if (sz[u] < sz[v]) swap(u, v);` luôn gắn cây có ít đỉnh hơn vào gốc của cây nhiều đỉnh hơn, tránh việc cây bị suy biến thành đường thẳng (skewed tree).
    * Nếu bỏ cả 2 tối ưu này, cây DSU có thể bị suy biến thành danh sách liên kết dài $O(N)$, làm mỗi thao tác `find` tốn $O(N)$ và tổng thời gian lên tới $O(M \cdot N) \approx 10^{10}$ phép toán $\rightarrow$ bị **TLE (Quá thời gian)**. Khi có cả 2 tối ưu, mỗi thao tác gần như đạt hằng số $O(\alpha(N)) \approx O(1)$.
* **Thầy cô hỏi:** *"Tại sao biến lưu kết quả `ans` phải khai báo kiểu `long long` trong khi trọng số mỗi cạnh `w` chỉ là `int`?"*
  * **Trả lời:** Vì cây khung có tới $N - 1 \approx 10^5$ cạnh. Nếu mỗi cạnh có trọng số lớn (ví dụ $w \approx 10^5$ hoặc $10^6$), tổng trọng số `ans` có thể lên tới $10^{10} - 10^{11}$, vượt quá giới hạn lưu trữ của kiểu `int` 32-bit ($\approx 2 \times 10^9$) và gây **tràn số nguyên (Integer Overflow)**.
* **Thầy cô hỏi:** *"Tại sao khi `cnt == n - 1` ta lại `break` dừng vòng lặp ngay? Giả sử đồ thị ban đầu không liên thông thì làm sao phát hiện được?"*
  * **Trả lời:** Một cây khung kết nối $N$ đỉnh luôn có chính xác $N - 1$ cạnh. Khi đã chọn đủ $N - 1$ cạnh không tạo chu trình nghĩa là tất cả $N$ đỉnh đã liên thông hoàn toàn, mọi cạnh xét thêm sau đó chắc chắn sẽ tạo chu trình nên `break` để tiết kiệm thời gian. Nếu đồ thị không liên thông, sau khi duyệt hết toàn bộ $M$ cạnh mà biến `cnt` vẫn nhỏ hơn `n - 1` (`cnt < n - 1`), ta kết luận đồ thị không tồn tại cây khung.
* **Thầy cô hỏi:** *"Độ phức tạp thời gian và không gian của thuật toán Kruskal trong bài này là bao nhiêu?"*
  * **Trả lời:**
    * **Thời gian:** Bước tốn thời gian nhất là sắp xếp $M$ cạnh mất $O(M \log M)$. Vòng lặp duyệt cạnh kết hợp DSU mất $O(M \cdot \alpha(N))$ (với $\alpha(N)$ là hàm Ackermann nghịch đảo, thực tế $\le 4$). Vậy tổng độ phức tạp thời gian là $O(M \log M)$.
    * **Không gian (Bộ nhớ):** $O(N + M)$ — gồm $O(M)$ cho `vector<Edge>` lưu danh sách cạnh và $O(N)$ cho 2 mảng `parent` và `sz`.

## 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

const int MAX = 100005;
int parent[MAX], sz[MAX];

// Cấu trúc lưu thông tin 1 cạnh: 2 đỉnh u, v và trọng số w
struct Edge {
    int u, v, w;
};

// Hàm so sánh để sắp xếp các cạnh theo trọng số tăng dần
bool cmp(Edge a, Edge b) {
    return a.w < b.w;
}

// Tìm gốc đại diện của tập hợp chứa u (có tối ưu Nén đường đi - Path Compression)
int find(int u) {
    if (parent[u] == u) return u;
    return parent[u] = find(parent[u]);
}

// Hợp nhất 2 tập hợp chứa u và v (có tối ưu Gộp theo kích thước - Union by Size)
bool Union(int u, int v) {
    u = find(u);
    v = find(v);

    if (u == v) return false; // u và v đã cùng tập hợp -> thêm cạnh sẽ tạo chu trình

    // Luôn gộp cây nhỏ (v) vào cây lớn (u)
    if (sz[u] < sz[v]) swap(u, v);
    parent[v] = u;
    sz[u] += sz[v];

    return true;
}

int main() {
    // Tối ưu tốc độ nhập xuất cho đồ thị lớn (N, M <= 10^5)
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m;
    cin >> n >> m;

    vector<Edge> e(m);
    for (int i = 0; i < m; i++)
        cin >> e[i].u >> e[i].v >> e[i].w;

    // Bước 1: Sắp xếp cạnh theo trọng số tăng dần - O(M log M)
    sort(e.begin(), e.end(), cmp);

    // Bước 2: Khởi tạo cấu trúc DSU cho n đỉnh
    for (int i = 1; i <= n; i++) {
        parent[i] = i;
        sz[i] = 1;
    }

    long long ans = 0; // Dùng long long tránh tràn số khi cộng dồn trọng số
    int cnt = 0;       // Đếm số cạnh đã được đưa vào cây khung

    // Bước 3: Duyệt các cạnh từ nhỏ đến lớn
    for (Edge x : e) {
        if (Union(x.u, x.v)) {
            ans += x.w;
            cnt++;

            // Cây khung đủ n - 1 cạnh thì dừng ngay
            if (cnt == n - 1) break;
        }
    }

    cout << ans << '\n';
    return 0;
}
```
# Bài 20: Duyệt đồ thị theo chiều rộng theo thứ tự từ điển (BFS - Breadth-First Search)

## 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu

* **Kỹ thuật:** Duyệt theo chiều rộng (**BFS - Breadth-First Search**) sử dụng Hàng đợi (`std::queue<int>`) kết hợp biểu diễn đồ thị bằng Danh sách kề (`vector<int> adj[]`).
* **Mục đích:**
  * Liệt kê thứ tự các đỉnh được thăm khi duyệt đồ thị vô hướng theo chiều rộng (ưu tiên thăm các đỉnh gần nguồn trước, lan rộng ra theo từng lớp khoảng cách).
  * Đảm bảo **thứ tự từ điển (lexicographical order)**: Nếu tại một bước có nhiều đỉnh kề cùng khoảng cách hoặc đồ thị có nhiều thành phần liên thông, luôn ưu tiên thăm đỉnh có chỉ số nhỏ hơn trước.
* **Logic hoạt động:**
  * **Bước 1 (Nhập & Sắp xếp danh sách kề):** Lưu đồ thị vô hướng vào danh sách kề `adj`. Sắp xếp từng danh sách `adj[i]` tăng dần bằng `sort(adj[i].begin(), adj[i].end())` để khi xét các đỉnh kề của `u`, đỉnh nhỏ hơn luôn được đẩy vào hàng đợi trước.
  * **Bước 2 (Xử lý đồ thị không liên thông):** Duyệt vòng lặp `i` từ `1` đến `n`, nếu đỉnh `i` chưa được thăm (`!vis[i]`) thì gọi `bfs(i)`. Cách này đảm bảo mọi thành phần liên thông đều được duyệt và luôn bắt đầu từ đỉnh nhỏ nhất của thành phần đó.
  * **Bước 3 (Cơ chế hàng đợi FIFO trong `bfs(s)`):**
    * Đẩy đỉnh xuất phát `s` vào `queue`, đánh dấu `vis[s] = true` ngay lập tức.
    * Lặp khi `queue` chưa rỗng: Lấy đỉnh `u` ở đầu hàng đợi ra (`q.front()`, `q.pop()`), in `u`, sau đó duyệt mọi đỉnh kề `v` của `u`. Nếu `v` chưa thăm thì đánh dấu `vis[v] = true` và đẩy `v` vào cuối hàng đợi.

## 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp

* **Thầy cô hỏi:** *"Tại sao phải đánh dấu `vis[v] = true` ngay lúc đẩy vào hàng đợi (`q.push(v)`), mà không đợi lúc lấy `v` ra khỏi hàng đợi (`u = q.front()`) mới đánh dấu như bên DFS?"*
  * **Trả lời:** Nếu đợi lúc lấy ra khỏi `queue` mới đánh dấu `vis[u] = true`, thì trong thời gian đỉnh `v` đang nằm chờ trong hàng đợi, các đỉnh khác cùng kề với `v` sẽ thấy `vis[v]` vẫn đang là `false` và tiếp tục đẩy `v` vào hàng đợi thêm nhiều lần nữa. Hậu quả là **một đỉnh bị in ra lặp lại nhiều lần** và gây tốn bộ nhớ/thời gian (**TLE / MLE**).
* **Thầy cô hỏi:** *"Tại sao trong hàm `main()` phải có vòng lặp `for (int i = 1; i <= n; i++) sort(adj[i].begin(), adj[i].end());`?"*
  * **Trả lời:** Vì thứ tự các cạnh trong dữ liệu đầu vào là ngẫu nhiên. Nếu không sắp xếp lại danh sách kề tăng dần, thuật toán sẽ đẩy các đỉnh vào `queue` theo thứ tự xuất hiện của cạnh trong input chứ không theo thứ tự đỉnh từ nhỏ đến lớn, làm sai kết quả nếu đề bài yêu cầu in theo **thứ tự từ điển**.
* **Thầy cô hỏi:** *"Tại sao trong `main()` không gọi thẳng `bfs(1)` mà phải dùng vòng lặp `for(int i = 1; i <= n; i++) if(!vis[i]) bfs(i);`?"*
  * **Trả lời:** Vì đồ thị tổng quát có thể **không liên thông** (gồm nhiều mảnh rời nhau). Nếu chỉ gọi `bfs(1)`, chương trình chỉ in được các đỉnh thuộc cùng thành phần liên thông với đỉnh `1` và bỏ sót các đỉnh ở các thành phần liên thông khác.
* **Thầy cô hỏi:** *"Điểm lỗi nhỏ (bug tiềm ẩn) trong khai báo `const int maxn = 1e5;` của đoạn code này là gì?"*
  * **Trả lời:** Đỉnh của đồ thị được đánh số từ `1` đến `n` (1-indexed). Nếu test case có đúng $n = 10^5$, việc truy cập `adj[100000]` và `vis[100000]` sẽ bị **tràn mảng (Out of Bounds)** vì mảng kích thước `100000` chỉ có chỉ số hợp lệ từ `0` đến `99999`. Cần sửa thành `const int maxn = 1e5 + 5;`.
* **Thầy cô hỏi:** *"Độ phức tạp thời gian của toàn bộ chương trình là bao nhiêu?"*
  * **Trả lời:** Gồm 2 phần:
    1. Sắp xếp toàn bộ danh sách kề: Với mỗi đỉnh $i$ có bậc $d_i$, sắp xếp mất $O(d_i \log d_i)$. Tổng cộng trên toàn đồ thị mất $O(M \log N)$ (do $\sum d_i = 2M$).
    2. Duyệt BFS: Mỗi đỉnh vào/ra hàng đợi đúng 1 lần, mỗi cạnh được xét 2 lần $\rightarrow$ mất $O(N + M)$.
    * Tổng độ phức tạp thời gian là $O(N + M \log N)$, không gian bộ nhớ là $O(N + M)$.

## 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

const int maxn = 1e5 + 5; // Nên cộng thêm 5 để tránh tràn mảng khi n = 10^5
vector<int> adj[maxn];
bool vis[maxn];

void bfs(int s) {
    queue<int> q;
    q.push(s);
    vis[s] = true; // Đánh dấu ngay khi đưa vào hàng đợi để tránh đẩy trùng
    
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        cout << u << " ";
        
        for (int v : adj[u]) {
            if (!vis[v]) {
                vis[v] = true; // Phải đánh dấu ngay tại đây
                q.push(v);
            }
        }
    }
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n, m;
    cin >> n >> m;
    for (int i = 0; i < m; i++) {
        int u, v;
        cin >> u >> v;
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    // Sắp xếp danh sách kề để đảm bảo duyệt theo thứ tự từ điển
    for (int i = 1; i <= n; i++) {
        sort(adj[i].begin(), adj[i].end());
    }

    // Duyệt qua tất cả các thành phần liên thông (nếu đồ thị bị đứt gãy)
    for (int i = 1; i <= n; i++) {
        if (!vis[i]) {
            bfs(i);
        }
    }

    return 0;
}
```

---

# Bài 21: Duyệt đồ thị theo chiều sâu theo thứ tự từ điển (DFS - Depth-First Search)

## 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu

* **Kỹ thuật:** Duyệt theo chiều sâu (**DFS - Depth-First Search**) sử dụng Đệ quy (ngầm định dùng Ngăn xếp lời gọi hàm - Call Stack) kết hợp Danh sách kề (`vector<int> adj[]`).
* **Mục đích:**
  * Liệt kê thứ tự các đỉnh được thăm khi duyệt đồ thị vô hướng theo chiều sâu bắt đầu từ đỉnh `1` (hoặc toàn bộ đồ thị).
  * Nguyên lý của DFS là đi sâu nhất có thể theo một nhánh cho đến khi chạm ngõ cụt (không còn đỉnh kề nào chưa thăm) thì mới quay lui lại để rẽ sang nhánh khác.
* **Logic hoạt động:**
  * **Bước 1 (Nhập & Chuẩn hóa thứ tự):** Đọc $M$ cạnh vô hướng vào danh sách kề `adj`. Sắp xếp từng danh sách `adj[i]` tăng dần để tại mỗi đỉnh `u`, thuật toán luôn ưu tiên đi sâu vào nhánh của đỉnh kề `v` có chỉ số nhỏ nhất trước.
  * **Bước 2 (Hàm đệ quy `DFS(u)`):**
    * Ngay khi bước vào đỉnh `u`: In `u` ra màn hình và đánh dấu `visited[u] = true`.
    * Duyệt qua từng đỉnh kề `v` trong `adj[u]`: Nếu `v` chưa được thăm (`!visited[v]`), lập tức gọi đệ quy `DFS(v)` (tạm dừng việc xét các đỉnh kề còn lại của `u` để đào sâu xuống nhánh của `v` trước).

## 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp

* **Thầy cô hỏi:** *"Sự khác biệt cốt lõi về cấu trúc dữ liệu và thứ tự duyệt giữa bài DFS này và bài BFS ở trên là gì?"*
  * **Trả lời:**
    * **Về cấu trúc dữ liệu:** BFS dùng Hàng đợi (**Queue** - cơ chế *FIFO: Vào trước ra trước*), còn DFS dùng Ngăn xếp (**Stack / Call Stack của đệ quy** - cơ chế *LIFO: Vào sau ra trước*).
    * **Về chiến lược duyệt:** BFS loang đều theo từng lớp khoảng cách tính từ đỉnh gốc (thăm hết các đỉnh kề trực tiếp rồi mới đi tiếp), trong khi DFS chọn 1 đỉnh kề đầu tiên và đi một mạch sâu nhất có thể cho tới khi hết đường mới quay lui.
* **Thầy cô hỏi:** *"So sánh hàm `main()` của bài DFS này với bài BFS ở trên, nếu đồ thị đầu vào không liên thông thì code DFS hiện tại sẽ gặp vấn đề gì?"*
  * **Trả lời:** Trong code DFS hiện tại chỉ gọi duy nhất lệnh `DFS(1);`, nên chỉ duyệt được các đỉnh nằm cùng thành phần liên thông với đỉnh `1`. Nếu đồ thị không liên thông và đề bài yêu cầu liệt kê đủ $N$ đỉnh như bài BFS, ta bắt buộc phải thay `DFS(1);` bằng vòng lặp:
    ```cpp
    for (int i = 1; i <= n; i++)
        if (!visited[i]) DFS(i);
    ```
* **Thầy cô hỏi:** *"Ở bài Quay lui tìm chu trình Hamilton (Bài 18), sau khi gọi đệ quy ta có dòng trả lại trạng thái `visited[v] = false;`. Tại sao trong hàm `DFS(u)` này lại KHÔNG có dòng `visited[v] = false`?"*
  * **Trả lời:**
    * Ở bài **Hamilton (Quay lui - Backtracking)**: Ta cần thử mọi đường đi có thể. Nếu đi theo một nhánh bị cụt, ta phải gỡ đánh dấu đỉnh `v` để nhánh khác có thể đi lại qua `v`. Độ phức tạp là hàm mũ/giai thừa $O(N!)$.
    * Ở bài **DFS duyệt đồ thị**: Mục tiêu chỉ là thăm mỗi đỉnh **đúng 1 lần** để liệt kê hoặc kiểm tra tính liên thông. Đỉnh nào đã thăm và duyệt xong hết các nhánh con của nó thì đóng lại vĩnh viễn (`visited = true`), nhờ vậy độ phức tạp mới giảm xuống mức tuyến tính $O(N + M)$.
* **Thầy cô hỏi:** *"Nếu đồ thị có dạng một đường thẳng dài $10^5$ đỉnh ($1 - 2 - 3 - \dots - 10^5$), việc dùng DFS đệ quy có nguy cơ gì?"*
  * **Trả lời:** Khi đó độ sâu đệ quy đạt tới $10^5$ tầng liên tiếp. Trên một số hệ thống chấm bài giới hạn bộ nhớ Ngăn xếp (Stack Memory Limit thấp, ví dụ trên Windows mặc định chỉ khoảng 1MB - 8MB), chương trình có thể bị lỗi **Tràn bộ nhớ ngăn xếp (Stack Overflow / Runtime Error)**. Cách khắc phục là khử đệ quy bằng cách tự khai báo `std::stack<int>` trên vùng nhớ Heap.

## 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, m;
vector<int> adj[100005];
bool visited[100005];

// Hàm đệ quy duyệt theo chiều sâu từ đỉnh u
void DFS(int u) {
    cout << u << " ";
    visited[u] = true; // Đánh dấu đỉnh u đã được thăm

    // Duyệt qua các đỉnh kề v của u (đã được sort tăng dần)
    for (int v : adj[u]) {
        if (!visited[v])
            DFS(v); // Đi sâu ngay lập tức vào nhánh của v
    }
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n >> m;

    for (int i = 0; i < m; i++) {
        int u, v;
        cin >> u >> v;
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    // Sắp xếp danh sách kề để ưu tiên đi vào đỉnh có chỉ số nhỏ hơn trước
    for (int i = 1; i <= n; i++)
        sort(adj[i].begin(), adj[i].end());

    // Bắt đầu duyệt DFS từ đỉnh 1
    // (Lưu ý: Nếu đề yêu cầu duyệt cả đồ thị không liên thông thì bọc trong vòng for 1 -> n)
    DFS(1);

    return 0;
}
```
# Bài 22: Đếm số lần xuất hiện của xâu mẫu P trong văn bản T (String Matching)

## 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu

* **Kỹ thuật:** Tìm kiếm xâu con liên tiếp sử dụng phương thức dựng sẵn `std::string::find` trong C++ STL kết hợp dịch chuyển vị trí bắt đầu tìm kiếm (`pos`).
* **Mục đích:**
  * Đếm tổng số lần xuất hiện của xâu mẫu $P$ (Pattern, độ dài $\le 10^5$) bên trong xâu văn bản $T$ (Text, độ dài $\le 10^6$), bao gồm cả các lần xuất hiện **gối đầu (chồng lấp) lên nhau**.
* **Logic hoạt động:**
  * **Bước 1 (Đọc dữ liệu có khoảng trắng):** Dùng `getline(cin, P)` và `getline(cin, T)` để đọc trọn vẹn cả dòng văn bản (vì chuỗi đầu vào chứa dấu cách).
  * **Bước 2 (Tìm kiếm lặp với mốc dịch chuyển `pos`):**
    * Khởi tạo biến đếm `dem = 0` và vị trí bắt đầu tìm kiếm `pos = 0`.
    * Hàm `T.find(P, pos)` sẽ tìm vị trí xuất hiện đầu tiên của xâu `P` trong `T` tính từ chỉ số `pos` trở đi.
    * Nếu tìm thấy (hàm trả về chỉ số khác hằng số `string::npos`), ta gán vị trí tìm được cho `pos`, tăng biến đếm `dem++`, sau đó nhích `pos` lên 1 đơn vị (`pos++`) để tiếp tục tìm kiếm lần xuất hiện tiếp theo trong vòng lặp `while`.
    * Khi không còn tìm thấy `P` nữa, `T.find(P, pos)` trả về `string::npos` và vòng lặp kết thúc.

## 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp

* **Thầy cô hỏi:** *"Tại sao khi đọc chuỗi `P` và `T` phải dùng `getline(cin, ...)` mà không dùng `cin >> P >> T`?"*
  * **Trả lời:** Toán tử `cin >>` sẽ coi các khoảng trắng (dấu cách `space`, dấu `tab`, xuống dòng) là ký tự phân cách và dừng đọc ngay khi gặp dấu cách đầu tiên. Như trong ví dụ đề bài, chuỗi `T` là cả một câu văn dài có nhiều khoảng trắng, nên bắt buộc phải dùng `getline` để lấy toàn bộ nội dung trên dòng đó.
* **Thầy cô hỏi:** *"Sau khi tìm thấy `P` tại vị trí `pos`, tại sao ta chỉ tăng `pos++` (nhích 1 ký tự) mà không cộng cả độ dài xâu mẫu `pos += P.length()`?"*
  * **Trả lời:** Vì bài toán đếm số lần xuất hiện của xâu con cho phép **các lần xuất hiện gối đầu (overlap) lên nhau**.
    * Ví dụ: Cho `T = "aaaa"` và `P = "aa"`.
    * Nếu dùng `pos++`: Tìm thấy ở chỉ số `0`, `1`, `2` $\rightarrow$ Kết quả đúng là `3` lần.
    * Nếu dùng `pos += P.length()`: Sau khi thấy ở chỉ số `0`, `pos` nhảy sang `2` và bỏ qua vị trí `1` $\rightarrow$ Kết quả chỉ ra `2` lần (bị thiếu nghiệm).
* **Thầy cô hỏi:** *"Hằng số `string::npos` có ý nghĩa là gì và bản chất kiểu dữ liệu của nó là gì?"*
  * **Trả lời:** `string::npos` (no position) là một hằng số tĩnh thuộc kiểu `size_t` (số nguyên không dấu - unsigned integer), mang giá trị bằng `-1` (tức giá trị lớn nhất của kiểu `size_t`). Hàm `find()` trả về `string::npos` để báo hiệu **không tìm thấy** xâu con trong phạm vi tìm kiếm.
* **Thầy cô hỏi:** *"Độ phức tạp của hàm `T.find(P, pos)` là bao nhiêu? Với $|P| \le 10^5$ và $|T| \le 10^6$, code này có điểm yếu gì trong trường hợp xấu nhất (Worst Case)?"*
  * **Trả lời:**
    * Hàm `std::string::find` cài đặt thuật toán vét cạn cải tiến, chạy cực nhanh (xấp xỉ $O(|T|)$) trên văn bản tiếng Anh thông thường.
    * Tuy nhiên, trong trường hợp suy biến (ví dụ `T = "aaaa...a"` gồm $10^6$ chữ `a` và `P = "aaa...ab"` gồm $10^5$ ký tự), độ phức tạp xấu nhất của `find` là $O(|T| \times |P|) \approx 10^{11}$ phép toán, có thể gây **TLE**. Để đảm bảo $O(|T| + |P|)$ tuyệt đối trong mọi test, ta có thể dùng thuật toán **KMP (Knuth-Morris-Pratt)** hoặc **Rolling Hash (Rabin-Karp)**.
* **Thầy cô hỏi:** *"Trên hệ điều hành Linux (môi trường chấm bài tự động), việc dùng `getline` đọc file test được tạo từ Windows đôi khi bị sai kết quả (`dem = 0`), tại sao?"*
  * **Trả lời:** File văn bản tạo trên Windows kết thúc dòng bằng cặp ký tự `\r\n`, trong khi Linux chỉ cắt bỏ `\n`. Khi đó cuối chuỗi `P` đọc bằng `getline` có thể bị dính thừa ký tự ẩn `\r` (carriage return), dẫn đến tìm không thấy trong `T`. Để an toàn tuyệt đối, sau `getline(cin, P)` ta có thể thêm: `if (!P.empty() && P.back() == '\r') P.pop_back();`.

## 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    string P, T;
    // Dùng getline để đọc trọn vẹn cả dòng có chứa dấu cách
    getline(cin, P);
    getline(cin, T);

    // Xử lý an toàn ký tự '\r' ở cuối dòng nếu test sinh trên Windows
    if (!P.empty() && P.back() == '\r') P.pop_back();
    if (!T.empty() && T.back() == '\r') T.pop_back();

    int dem = 0;
    size_t pos = 0;

    // Tìm P trong T bắt đầu từ vị trí pos cho đến khi không tìm thấy (string::npos)
    while ((pos = T.find(P, pos)) != string::npos) {
        dem++;
        pos++; // Tăng pos lên 1 để xét cả các trường hợp xâu P gối đầu lên nhau
    }

    cout << dem << '\n';
    return 0;
}
```
