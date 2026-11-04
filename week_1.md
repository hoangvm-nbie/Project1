# CẨM NANG ÔN TẬP VẤN ĐÁP DSA (8 TUẦN)

---

## BÀI 1: Sequence Queries & Range Max Segment (Sparse Table / RMQ)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Bài toán yêu cầu:** Thực hiện các truy vấn trên mảng: tính Tổng (`sum`), Max toàn mảng (`find-max`), Min toàn mảng (`find-min`), và Max đoạn $[i, j]$ (`find-max-segment`).
* **Giải thuật chính:** Sử dụng **Sparse Table (Bảng thưa)** để xử lý bài toán **RMQ (Range Maximum Query)**.
  * **Tiền xử lý (Preprocessing):** Xây dựng bảng `st[j][i]` lưu giá trị Max trong đoạn độ dài $2^j$ bắt đầu từ vị trí $i$. Độ phức tạp: $O(N \log N)$.
  * **Truy vấn (Query):** Trả về Max đoạn $[l, r]$ trong thời gian $O(1)$ nhờ tính chất chồng lấp (Overlap Property) của phép `max`: lấy max của 2 đoạn con độ dài $2^k$ bao phủ toàn bộ $[l, r]$, với $k = \lfloor \log_2(r - l + 1) \rfloor$.
* **Độ phức tạp:**
  * **Thời gian:** Khởi tạo $O(N \log N)$, truy vấn đoạn $[l, r]$ là $O(1)$, truy vấn toàn mảng là $O(1)$.
  * **Không gian (Bộ nhớ):** $O(N \log N)$ cho mảng `st[15][10005]`.

---

### 2. Các câu hỏi vấn đáp thường gặp & Bẫy (Trap Questions)
* **Q1: Tại sao bài này dùng Sparse Table mà không dùng Segment Tree?**
  * *Trả lời:* Mảng dữ liệu tĩnh, không có thao tác cập nhật (Update). Dùng Sparse Table cho thời gian truy vấn $O(1)$ cực nhanh, nhanh hơn $O(\log N)$ của Segment Tree.
* **Q2: Tại sao hàm `query(l, r)` chỉ lấy `max` của 2 đoạn mà vẫn phủ hết đoạn $[l, r]$?**
  * *Trả lời:* Do độ dài 2 đoạn là $2^k$ (với $2^k > (r - l + 1)/2$), hai đoạn $[l, l + 2^k - 1]$ và $[r - 2^k + 1, r]$ sẽ ghi đè/chồng lấp lên nhau ở giữa, đảm bảo không bỏ sót phần tử nào. Phép `max` cho phép các phần tử trùng lặp mà không ảnh hưởng kết quả.
* **Q3: Bẫy nhập liệu (Input Parsing):**
  * Lưu ý ký tự kết thúc khối 1 là `*` (được đọc qua `cin >> s` ngay sau vòng lặp đọc mảng) và kết thúc khối 2 là `***`.

---

### 3. Code cốt lõi (Core Snippet)

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 10005;
int a[N], st[15][N], lg[N];

// Xây dựng Sparse Table O(N log N)
void build(int n) {
    lg[1] = 0;
    for (int i = 2; i <= n; i++) lg[i] = lg[i / 2] + 1; // Tiền tính log2
    
    for (int i = 1; i <= n; i++) st[0][i] = a[i]; // Đáy DP: đoạn độ dài 2^0 = 1
    
    for (int j = 1; (1 << j) <= n; j++)
        for (int i = 1; i + (1 << j) - 1 <= n; i++)
            st[j][i] = max(st[j - 1][i], st[j - 1][i + (1 << (j - 1))]);
}

// Truy vấn Max đoạn [l, r] trong O(1)
int query(int l, int r) {
    int k = lg[r - l + 1];
    return max(st[k][l], st[k][r - (1 << k) + 1]);
}
```
---

## Bài 2: So sánh tổng tiền gửi ngân hàng (Range Sum Comparison)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Mảng cộng dồn (**Prefix Sum**).
* **Mục đích:** Tính tổng các phần tử trong đoạn $[i, j]$ chỉ mất độ phức tạp **$O(1)$** sau bước tiền xử lý $O(n)$.
* **Công thức cốt lõi:** 
  $$\text{Sum}(i, j) = S[j] - S[i - 1]$$
  *(với $S[k]$ là tổng các phần tử từ $1$ đến $k$, quy ước $S[0] = 0$).*

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao không dùng vòng lặp `for` chạy từ $i$ đến $j$ để tính tổng trực tiếp?"*
  * **Trả lời:** Có $m = 10^6$ truy vấn, nếu tính trực tiếp mỗi truy vấn mất $O(n)$ $\rightarrow$ Tổng độ phức tạp $O(m \times n) \approx 10^{11}$ phép tính $\rightarrow$ **TLE (Quá thời gian)**. Dùng Prefix Sum giảm xuống $O(n + m)$.
* **Thầy cô hỏi:** *"Kiểu dữ liệu có bị tràn số (Overflow) không?"*
  * **Trả lời:** $n \le 10^5$, $a[i] \le 1000 \rightarrow$ Tổng tối đa là $10^8$, vẫn nằm trong giới hạn của `int` ($2 \times 10^9$). Tuy nhiên khai báo `long long` là an toàn tuyệt đối.
* **Lưu ý chỉ số:** Dãy số đánh số từ $1$ đến $n$, nên mảng cộng dồn phải để $S[0] = 0$ để xử lý trường hợp $i = 1$ mà không bị truy cập chỉ số âm.

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <iostream>
using namespace std;

int main() {
    // Tối ưu I/O cho số lượng truy vấn lớn (10^6)
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n;
    cin >> n;

    long long S[100005];
    S[0] = 0;

    for (int i = 1; i <= n; i++) {
        int a;
        cin >> a;
        S[i] = S[i - 1] + a; // Xây dựng mảng cộng dồn O(N)
    }

    int m;
    cin >> m;

    while (m--) {
        int i1, j1, i2, j2;
        cin >> i1 >> j1 >> i2 >> j2;

        // Truy vấn O(1)
        long long sum1 = S[j1] - S[i1 - 1];
        long long sum2 = S[j2] - S[i2 - 1];

        cout << (sum1 < sum2 ? 1 : 0) << '\n';
    }

    return 0;
}
```
---

## Bài 3: Đếm số từ trong văn bản (Word Count)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Duyệt chuỗi kết hợp cờ hiệu trạng thái (**State Flag - biến `inWord`**).
* **Mục đích:** Đếm chính xác số lượng từ phân tách bởi khoảng trắng (`SPACE`, `TAB`, `LineBreak`), xử lý được các trường hợp thừa khoảng trắng liên tiếp hoặc xuống dòng.
* **Logic hoạt động:**
  * Duyệt từng ký tự của dòng đọc được.
  * Nếu gặp ký tự trắng (` `, `\t`), đánh dấu `inWord = false`.
  * Nếu gặp ký tự thường và `inWord` đang là `false` $\rightarrow$ Tăng biến đếm `Q++` (bắt đầu một từ mới) và chuyển `inWord = true`.
  * Hết mỗi dòng, reset `inWord = false` (vì kí tự xuống dòng `LineBreak` cũng ngắt từ).

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao đoạn cuối mỗi vòng lặp `getline` lại gán `inWord = false`?"*
  * **Trả lời:** Ký tự xuống dòng (`\n`) được `getline` lược bỏ, nhưng thực tế nó đóng vai trò ngắt từ giữa dòng trước và dòng sau. Nếu không reset `inWord = false` ở cuối dòng, từ cuối dòng trước và từ đầu dòng sau sẽ bị dính liền làm một (đếm nhầm thành 1 từ).
* **Thầy cô hỏi:** *"Có cách nào ngắn hơn thay vì dùng biến `inWord` thủ công không?"*
  * **Trả lời:** Có thể dùng `stringstream` để đọc từng từ qua toán tử `cin >> s`, vì `stringstream` tự động bỏ qua mọi khoảng trắng và ký tự xuống dòng. Tuy nhiên cách dùng `stringstream` đôi khi tốn bộ nhớ hơn với file text cực lớn, còn cách duyệt cờ hiệu đạt hiệu suất $O(N)$ tối ưu.

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    // Tối ưu tốc độ đọc file/chuỗi đầu vào lớn
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    string s;
    int Q = 0;
    bool inWord = false; // Cờ hiệu kiểm tra xem có đang ở trong từ hay không

    // Đọc từng dòng cho đến hết file (EOF)
    while (getline(cin, s)) {
        for (int i = 0; i < s.length(); i++) {
            if (s[i] == ' ' || s[i] == '\t') {
                inWord = false;
            } else {
                if (!inWord) {
                    Q++;         // Bắt đầu 1 từ mới
                    inWord = true;
                }
            }
        }
        inWord = false; // Xuống dòng cũng coi như ngắt từ
    }

    cout << Q << "\n";

    return 0;
}
```
---

## Bài 4: Kiểm tra và tách định dạng ngày tháng (Date Validation & Extraction)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Xử lý chuỗi (**String Parsing**) kết hợp hàm kiểm tra logic (**Validation Function**).
* **Mục đích:** Xác thực cấu trúc chuỗi có đúng chuẩn `YYYY-MM-DD` không, kiểm tra tính hợp lệ của giá trị tháng/ngày, sau đó tách và in ra năm, tháng, ngày.
* **Logic kiểm tra (`check` function):**
  1. Kiểm tra độ dài chuỗi bắt buộc bằng `10`.
  2. Kiểm tra dấu gạch ngang tại đúng vị trí cố định (`s[4] == '-'` và `s[7] == '-'`).
  3. Kiểm tra các ký tự tại vị trí số phải là chữ số (`isdigit`).
  4. Chuyển đổi chuỗi thành số nguyên (`stoi`) và giới hạn phạm vi giá trị: $m \in [1, 12]$, $d \in [1, 31]$, năm $y \ge 1$.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao cần xử lý `s.back() == '\r'` ở đầu hàm `main`?"*
  * **Trả lời:** Khi chạy chương trình trên các hệ điều hành Windows, tệp đầu vào có thể sử dụng ký tự xuống dòng kiểu DOS (`\r\n`). Lệnh `getline` có thể đọc dư ký tự `\r` ở cuối chuỗi, làm chuỗi dài hơn $10$ ký tự và dẫn đến kết luận nhầm là `INCORRECT`. Dòng lệnh này giúp loại bỏ ký tự ẩn đó để đảm bảo tính đúng đắn.
* **Thầy cô hỏi:** *"Hàm `check` dùng tham chiếu (`int &y, int &m, int &d`) có tác dụng gì?"*
  * **Trả lời:** Giúp hàm trả về giá trị kiểu `bool` (hợp lệ hay không), đồng thời truyền ngược trực tiếp các giá trị năm, tháng, ngày đã bóc tách ra ngoài hàm `main` mà không cần dùng cấu trúc dữ liệu phức tạp hoặc con trỏ thủ công.

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

bool check(const string &s, int &y, int &m, int &d) {
    // 1. Kiểm tra độ dài và vị trí dấu gạch ngang
    if (s.length() != 10) return false;
    if (s[4] != '-' || s[7] != '-') return false;

    // 2. Kiểm tra các ký tự số tại vị trí YYYY, MM, DD
    for (int i : {0, 1, 2, 3, 5, 6, 8, 9}) {
        if (!isdigit(s[i])) return false;
    }

    // 3. Tách chuỗi thành số nguyên
    y = stoi(s.substr(0, 4));
    m = stoi(s.substr(5, 2));
    d = stoi(s.substr(8, 2));

    // 4. Kiểm tra miền giá trị hợp lệ của tháng và ngày
    if (y < 1 || m < 1 || m > 12 || d < 1 || d > 31) return false;

    return true;
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    string s;
    if (!getline(cin, s)) return 0;
    
    // Xử lý ký tự xuống dòng thừa trên Windows (\r)
    if (!s.empty() && s.back() == '\r') s.pop_back();

    int y, m, d;
    if (check(s, y, m, d)) {
        cout << y << " " << m << " " << d << "\n";
    } else {
        cout << "INCORRECT\n";
    }

    return 0;
}
```
---

## Bài 5: Đếm số k-subsequence có tổng chẵn (Sliding Window)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Cửa sổ trượt (**Sliding Window**).
* **Mục đích:** Tính tổng của các đoạn con có độ dài cố định $k$ liên tiếp mà không phải tính lại từ đầu cho mỗi đoạn, giúp tối ưu độ phức tạp thời gian từ $O(n \times k)$ xuống còn **$O(n)$**.
* **Logic hoạt động:**
  1. Tính tổng của cửa sổ đầu tiên gồm $k$ phần tử từ chỉ số $0$ đến $k-1$.
  2. Trượt cửa sổ sang phải từng bước một: Cộng phần tử mới vào (`a[i]`) và trừ phần tử cũ ở đầu cửa sổ ra (`a[i - k]`).
  3. Kiểm tra tính chẵn lẻ của tổng (`cur_sum % 2 == 0`) tại mỗi bước dịch chuyển để tăng biến đếm `count`.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao bài này không dùng mảng cộng dồn (Prefix Sum) mà lại dùng Sliding Window?"*
  * **Trả lời:** Dùng Prefix Sum vẫn giải được với độ phức tạp $O(n)$, tuy nhiên Sliding Window có không gian trạng thái tối ưu hơn (chỉ cần duy trì một biến `cur_sum` thay vì tạo hẳn một mảng $S$ kích thước $n$), đồng thời trực quan hơn khi duyệt qua các phần tử liên tiếp có kích thước cố định $k$.
* **Thầy cô hỏi:** *"Biến đếm `count` có cần dùng kiểu dữ liệu `long long` không?"*
  * **Trả lời:** Số lượng $k$-subsequence tối đa là $n - k + 1 \approx 10^5$, vẫn nằm trong giới hạn của kiểu `int` ($2 \times 10^9$). Tuy nhiên, tổng `cur_sum` có thể lên tới $10^5 \times 10^4 = 10^9$ (vượt quá giới hạn `int` chuẩn nếu các phần tử lớn hơn hoặc có số lượng nhiều hơn), nên `cur_sum` dùng kiểu `long long` là hoàn toàn chính xác để tránh tràn số.

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n, k;
    if (!(cin >> n >> k)) return 0;

    vector<int> a(n);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    // 1. Tính tổng cửa sổ đầu tiên độ dài k
    long long cur_sum = 0;
    for (int i = 0; i < k; i++) {
        cur_sum += a[i];
    }

    int count = 0;
    if (cur_sum % 2 == 0) count++;

    // 2. Trượt cửa sổ qua các phần tử còn lại O(N)
    for (int i = k; i < n; i++) {
        cur_sum += a[i] - a[i - k]; // Thêm phần tử mới, bớt phần tử cũ
        if (cur_sum % 2 == 0) {
            count++;
        }
    }

    cout << count << "\n";

    return 0;
}
```
---

## Bài 6: Thay thế xâu ký tự (String Find and Replace)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Sử dụng các phương thức xử lý chuỗi có sẵn trong Thư viện chuẩn C++ (`string::find` và `string::replace`).
* **Mục đích:** Tìm tất cả các vị trí xuất hiện của xâu mẫu $P_1$ trong văn bản $T$ và thay thế chúng bằng xâu $P_2$.
* **Logic hoạt động:**
  1. Dùng `T.find(P1)` để tìm vị trí xuất hiện đầu tiên của $P_1$ trong $T$.
  2. Nếu tìm thấy (`pos != string::npos`), dùng `T.replace(pos, P1.length(), P2)` để thay thế đoạn từ vị trí `pos` có độ dài bằng `P1.length()` thành xâu $P_2$.
  3. **Điểm mấu chốt (Tránh lặp vô hạn):** Tiếp tục tìm kiếm vị trí tiếp theo bắt đầu từ `pos + P2.length()` thay vì `pos + 1`. Việc này giúp tránh trường hợp xâu $P_2$ chứa chính xâu $P_1$ bên trong nó, dẫn đến việc tìm lại đúng vị trí vừa thay thế và gây ra vòng lặp vô hạn (Infinite Loop).

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao tham số tìm kiếm tiếp theo lại là `pos + P2.length()` thay vì `pos + 1` hay `pos + P1.length()`?"*
  * **Trả lời:** 
    * Nếu dùng `pos + 1`, khi $P_2$ có chứa ký tự đầu của $P_1$, chương trình có thể match nhầm đè lên chính phần vừa thay thế.
    * Nếu dùng `pos + P2.length()`, ta dịch con trỏ qua toàn bộ phần vừa được thay thế bằng $P_2$, đảm bảo an toàn tuyệt đối và chỉ tìm các kết quả mới ở phần sau của văn bản.
* **Thầy cô hỏi:** *"Nếu xâu $P_1$ không xuất hiện trong $T$ thì hàm `find` trả về giá trị gì?"*
  * **Trả lời:** Trả về hằng số `string::npos` (thường có giá trị là `size_t(-1)`, tức là số nguyên cực lớn). Vòng `while` sẽ không chạy và giữ nguyên văn bản $T$ ban đầu.

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    string P1, P2, T;
    getline(cin, P1);
    getline(cin, P2);
    getline(cin, T);

    // Tìm vị trí xuất hiện đầu tiên của P1 trong T
    size_t pos = T.find(P1);

    // Vòng lặp thay thế tất cả các lần xuất hiện
    while (pos != string::npos) {
        // Thay thế P1 bằng P2 tại vị trí pos
        T.replace(pos, P1.length(), P2);
        
        // Tìm lần xuất hiện tiếp theo, nhảy qua phần vừa thay thế (pos + P2.length())
        pos = T.find(P1, pos + P2.length());
    }

    cout << T << "\n";

    return 0;
}
