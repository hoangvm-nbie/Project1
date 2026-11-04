---

## Bài 7: Sinh dãy nhị phân không có 2 số 1 liên tiếp (Backtracking)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Quay lui (**Backtracking** / Đệ quy sinh cấu hình).
* **Mục đích:** Liệt kê tất cả các dãy nhị phân độ dài $n$ theo thứ tự từ điển (lexicographic order) thỏa mãn điều kiện không có hai chữ số `1` đứng cạnh nhau (`11`).
* **Logic hoạt động:**
  1. Xây dựng từng vị trí `i` từ `1` đến `n`.
  2. Tại mỗi vị trí `i`, ưu tiên thử giá trị `0` trước (`a[i] = 0`), sau đó gọi đệ quy `Try(i + 1)`. Vì `0 < 1` nên việc thử `0` trước đảm bảo kết quả sinh ra hoàn toàn tuân theo **thứ tự từ điển**.
  3. Chỉ thử giá trị `1` (`a[i] = 1`) nếu thỏa mãn điều kiện ràng buộc: Hoặc là vị trí đầu tiên (`i == 1`), hoặc phần tử đứng trước nó mang giá trị `0` (`a[i-1] == 0`). Điều này chặn đứng nhánh tạo ra hai số `1` liên tiếp.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao thứ tự gọi đệ quy (thử `0` trước, thử `1` sau) lại giúp in ra đúng thứ tự từ điển?"*
  * **Trả lời:** Thứ tự từ điển so sánh từ trái qua phải, giá trị nhỏ hơn đứng trước (`0` nhỏ hơn `1`). Bằng cách gán `a[i] = 0` và gọi `Try(i+1)` trước, thuật toán luôn vét cạn các trường hợp bắt đầu bằng `0` ở vị trí hiện tại trước khi chuyển sang nhánh bắt đầu bằng `1`, qua đó tự động đảm bảo tính thứ tự từ điển.
* **Thầy cô hỏi:** *"Độ phức tạp thời gian của bài toán này là bao nhiêu? Có bị quá thời gian (TLE) với $n = 20$ không?"*
  * **Trả lời:** Số lượng cấu hình thỏa mãn điều kiện không có hai số `1` liên tiếp chính là số Fibonacci thứ $n+2$ ($F_{n+2}$). Với $n \le 20$, $F_{22} = 17711$ cấu hình, số lượng cực kỳ nhỏ nên thuật toán chạy hoàn toàn tức thời (gần như $O(1)$ thực tế).

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int a[25];
int n;

void Try(int i) {
    // Điều kiện dừng: Đã xây dựng xong đủ n vị trí
    if (i > n) {
        for (int j = 1; j <= n; j++) {
            cout << a[j];
        }
        cout << "\n";
        return;
    }

    // Nhánh 1: Thử gán giá trị 0 (luôn hợp lệ vì không tạo thành cặp 11)
    a[i] = 0;
    Try(i + 1);

    // Nhánh 2: Thử gán giá trị 1 (chỉ hợp lệ nếu phần tử trước đó là 0 hoặc ở vị trí đầu)
    if (i == 1 || a[i - 1] == 0) {
        a[i] = 1;
        Try(i + 1);
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n;
    Try(1); // Bắt đầu sinh từ vị trí số 1

    return 0;
}
```
tính ckn
```
#include <bits/stdc++.h>
using namespace std;

const long long MOD = 1000000007;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int k, n;
    cin >> k >> n;

    if (k > n)
    {
        cout << 0;
        return 0;
    }

    long long C[1000][1000] = {};

    for (int i = 0; i <= n; i++)
    {
        C[0][i] = 1;
        C[i][i] = 1;
    }

    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j < i; j++)
        {
            C[j][i] = (C[j - 1][i - 1] + C[j][i - 1]) % MOD;
        }
    }

    cout << C[k][n];

    return 0;
}
```
---

## Bài 8: Đếm số nghiệm Sudoku (Sudoku Solver & Counter)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Quay lui kết hợp vét cạn (**Backtracking / DFS**).
* **Mục đích:** Tìm và đếm tất cả các cách điền các số từ $1$ đến $9$ vào các ô trống (`0`) của bảng Sudoku $9 \times 9$ thỏa mãn điều kiện: Các số trên mỗi hàng, mỗi cột và mỗi ô vuông con $3 \times 3$ đều phân biệt (không trùng nhau).
* **Logic hoạt động:**
  1. Hàm `Try()` quét tìm ô trống đầu tiên (có giá trị `0`) trên bảng từ trái qua phải, từ trên xuống dưới.
  2. Nếu tìm thấy ô trống `(i, j)`, dùng vòng lặp thử lần lượt các giá trị `num` từ $1$ đến $9$.
  3. Hàm `check(row, col, num)` kiểm tra xem việc điền `num` vào ô `(i, j)` có hợp lệ không (không bị trùng trên hàng, cột và ô $3 \times 3$ chứa nó).
  4. Nếu hợp lệ, gán `a[i][j] = num` và gọi đệ quy `Try()` để tiếp tục giải cho các ô trống tiếp theo. Sau khi gọi đệ quy xong phải **phục hồi trạng thái** (`a[i][j] = 0`) để quay lui thử các giá trị khác.
  5. Nếu quét toàn bộ bảng mà không còn ô `0` nào nữa $\rightarrow$ Tìm thấy một lời giải hợp lệ, tăng biến đếm `ans++`.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao sau câu lệnh `Try();` trong vòng lặp thử số lại phải có dòng `a[i][j] = 0;` (Backtrack)?"*
  * **Trả lời:** Đây là bản chất cốt lõi của thuật toán quay lui. Khi một nhánh đệ quy đi sâu xuống dưới nhưng gặp cấu hình sai (không tìm được lời giải ở các bước sau), chương trình phải trả ô đó về giá trị `0` ban đầu để trả lại trạng thái sạch cho bảng, từ đó mới có thể thử các giá trị `num` tiếp theo hoặc quay về các ô trước đó. Nếu thiếu bước này, bảng sẽ bị ghi đè rác và bỏ sót nghiệm.
* **Thầy cô hỏi:** *"Hàm `check` kiểm tra ô vuông $3 \times 3$ được viết như thế nào?"*
  * **Trả lời:** Tọa độ góc trên bên trái của ô $3 \times 3$ chứa ô `(row, col)` được tính bằng công thức: `startrow = row / 3 * 3` và `startcol = col / 3 * 3`. Từ đó dùng hai vòng lặp duyệt qua đúng 9 ô trong phạm vi `[startrow, startrow + 2]` và `[startcol, startcol + 2]` để đối chiếu giá trị.

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int a[9][9];
long long ans = 0; // Biến đếm tổng số nghiệm

// Hàm kiểm tra tính hợp lệ khi điền số num vào ô (row, col)
bool check(int row, int col, int num) {
    // 1. Kiểm tra hàng
    for (int i = 0; i < 9; i++) {
        if (a[row][i] == num) return false;
    }
    // 2. Kiểm tra cột
    for (int i = 0; i < 9; i++) {
        if (a[i][col] == num) return false;
    }
    // 3. Kiểm tra ô vuông con 3x3
    int startcol = col / 3 * 3;
    int startrow = row / 3 * 3;
    for (int i = startcol; i < startcol + 3; i++) {
        for (int j = startrow; j < startrow + 3; j++) {
            if (a[j][i] == num) return false;
        }
    }
    return true;
}

// Hàm quay lui vét cạn tìm nghiệm
void Try() {
    for (int i = 0; i < 9; i++) {
        for (int j = 0; j < 9; j++) {
            // Tìm thấy ô trống
            if (a[i][j] == 0) {
                for (int num = 1; num <= 9; num++) {
                    if (check(i, j, num)) {
                        a[i][j] = num;  // Thử điền số num
                        Try();            // Đệ quy giải tiếp
                        a[i][j] = 0;    // Phục hồi trạng thái (Backtrack)
                    }
                }
                return; // Trả về khi đã vét cạn các số tại ô trống này
            }
        }
    }
    // Nếu không còn ô trống nào -> Tìm thấy 1 lời giải hoàn chỉnh
    ans++;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    for (int i = 0; i < 9; i++) {
        for (int j = 0; j < 9; j++) {
            cin >> a[i][j];
        }
    }

    Try();

    cout << ans << "\
```
---

## Bài 9: Phân công giảng dạy cân bằng (Teacher Course Assignment)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Quay lui kết hợp nhánh cận (**Backtracking with Branch and Bound**).
* **Mục đích:** Phân bổ $n$ môn học cho $m$ giáo viên sao cho thỏa mãn các ràng buộc khắt khe:
  1. Mỗi giáo viên chỉ dạy các môn nằm trong danh mục chuyên môn của mình (`canTeach`).
  2. Số lượng môn học phân cho mỗi giáo viên $t$ phải đạt tối thiểu mức `LB[t]`.
  3. Không có hai môn học xung đột lịch học (`conflict`) nào được phân cho cùng một giáo viên.
  4. **Tối ưu:** Giảm thiểu tải trọng lớn nhất của các giáo viên ($\min(\max \text{load})$).
* **Heuristic sắp xếp (Sắp xếp ưu tiên):** 
  * Mảng `order` được sắp xếp trước bằng hàm `cmp`: Ưu tiên xếp các môn có ít giáo viên có khả năng dạy nhất (`cntX < cntY`) trước, nếu bằng nhau thì ưu tiên môn có số tín chỉ lớn hơn (`credit[x] > credit[y]`). Điều này giúp vét cạn nhánh sai sớm hơn, tăng tốc độ cắt tỉa.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao trong hàm `check`, vòng lặp kiểm tra xung đột lại chạy từ `i = 1` đến `n` (hoặc qua các môn đã xét) thay vì chỉ kiểm tra các môn đã gán trước đó?"*
  * **Trả lời:** Thuật toán duyệt gán từng môn theo mảng `order`. Tại thời điểm xét môn ở vị trí `pos`, các môn `order[i]` với $i < pos$ đã được phân công vào các giáo viên (`teacher[c]`). Ta kiểm tra xem môn `c` đó có được giao cho cùng giáo viên `t` và có xung đột với môn hiện tại (`conflict[c][course]`) hay không.
* **Thầy cô hỏi:** *"Chiến lược nhánh cận (Bounding) trong hàm `Try` được thể hiện ở đoạn nào?"*
  * **Trả lời:** Dòng lệnh `if (load[t] + credit[course] >= ans) continue;`. Nếu tải trọng hiện tại của giáo viên $t$ cộng thêm tín chỉ của môn đang xét mà đã vượt quá hoặc bằng giá trị `ans` tối ưu tốt nhất tìm được trước đó, ta lập tức cắt nhánh (`pruning`) không đi sâu tiếp, giúp tiết kiệm thời gian đáng kể.
* **Thầy cô hỏi:** *"Điều kiện kiểm tra `LB[t]` được thực hiện ở đâu?"*
  * **Trả lời:** Được kiểm tra tại điều kiện dừng của đệ quy (`pos > n`), qua một vòng lặp quét qua tất cả giáo viên để đảm bảo không có giáo viên nào bị thiếu số lượng môn tối thiểu (`cnt[t] < LB[t]`).

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

int m, n;
int credit[31];
int LB[11];
bool canTeach[11][31];
bool conflict[31][31];

int teacher[31];
int load[11];
int cnt[11];
int order[31];

int ans = 1000000000;

// Hàm kiểm tra ràng buộc phân công môn cho giáo viên t
bool check(int course, int t) {
    if (!canTeach[t][course]) return false;
    
    // Kiểm tra xung đột với các môn đã được xếp trước đó
    for (int i = 1; i <= n; i++) {
        int c = order[i];
        if (teacher[c] == t && conflict[c][course])
            return false;
    }
    return true;
}

// Sắp xếp ưu tiên môn khó xếp trước (ít GV dạy, tín chỉ lớn)
bool cmp(int x, int y) {
    int cntX = 0, cntY = 0;
    for (int t = 1; t <= m; t++) {
        if (canTeach[t][x]) cntX++;
        if (canTeach[t][y]) cntY++;
    }
    if (cntX != cntY) return cntX < cntY;
    return credit[x] > credit[y];
}

// Hàm quay lui vét cạn phân công môn học
void Try(int pos) {
    if (pos > n) {
        // Kiểm tra ràng buộc tối thiểu LB[t] cho từng giáo viên
        for (int t = 1; t <= m; t++) {
            if (cnt[t] < LB[t]) return;
        }

        // Tính maxLoad của cấu hình hiện tại
        int maxLoad = 0;
        for (int t = 1; t <= m; t++) {
            maxLoad = max(maxLoad, load[t]);
        }
        ans = min(ans, maxLoad);
        return;
    }

    int course = order[pos];
    for (int t = 1; t <= m; t++) {
        if (!check(course, t)) continue;
        
        // Nhánh cận: Nếu tải hiện tại đã >= ans thì cắt nhánh
        if (load[t] + credit[course] >= ans) continue;
```
---

## Bài 10: Tính giá trị biểu thức toán học và kiểm tra cú pháp (Expression Evaluation)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Phân tích cú pháp một chiều (**Single-pass Parsing**) kết hợp tuân thủ quy tắc ưu tiên toán tử (`*` có độ ưu tiên cao hơn `+`).
* **Mục đích:** 
  1. Kiểm tra tính hợp lệ về mặt cú pháp của biểu thức chỉ chứa các toán hạng số nguyên dương và hai toán tử `+`, `*` (Ví dụ: Không được có hai toán tử liên tiếp, không bắt đầu/kết thúc bằng toán tử không hợp lệ, v.v.).
  2. Tính giá trị biểu thức theo modulo $10^9 + 7$ trong một lần duyệt tuyến tính $O(N)$ mà không cần xây dựng cây cú pháp hoặc dùng thuật toán ký pháp Ba Lan ngược (RPN) phức tạp.
* **Logic hoạt động:**
  * Duy trì hai biến: `sum` (lưu tổng cộng dồn của các cụm tích) và `product` (lưu tích hiện tại đang được nhân dồn theo thứ tự ưu tiên của phép `*`).
  * Sử dụng cờ hiệu `needNumber` để kiểm soát trạng thái: Bắt buộc phải là số hay phải là toán tử, qua đó phát hiện lỗi cú pháp ngay lập tức khi gặp ký tự bất thường hoặc sai vị trí.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao bài này phép nhân `*` lại được tính trước phép cộng `+` mà không cần dùng cấu trúc dữ liệu Stack?"*
  * **Trả lời:** Do biểu thức chỉ chứa hai phép toán `+` và `*`, phép `*` có độ ưu tiên cao hơn và tính liên tục từ trái qua phải giữa các số. Ta nhóm các số nhân liên tiếp vào biến `product`. Khi gặp dấu `+`, ta cộng dồn `product` hiện tại vào `sum` và reset lại `product = 1` để bắt đầu cụm nhân mới. Đến cuối cùng cộng nốt `product` còn lại vào `sum`. Cách này vừa tối ưu $O(N)$ thời gian vừa tiết kiệm bộ nhớ.
* **Thầy cô hỏi:** *"Làm thế nào đoạn code phát hiện được biểu thức sai cú pháp (ví dụ: `5*+7` hoặc kết thúc bằng dấu `+` như `5+`)?"*
  * **Trả lời:** 
    * Kiểm tra ngay tại biến `needNumber`: Nếu đang cần số mà ký tự hiện tại không phải chữ số $\rightarrow$ Báo lỗi `NOT_CORRECT`.
    * Kiểm tra cuối vòng lặp (`if (needNumber)`): Nếu chuỗi kết thúc bằng một toán tử (vẫn đang chờ một số tiếp theo) $\rightarrow$ Báo lỗi `NOT_CORRECT`.
* **Thầy cô hỏi:** *"Việc tính toán với modulo $10^9 + 7$ cần lưu ý điều gì về kiểu dữ liệu?"*
  * **Trả lời:** Các phép nhân và cộng số lớn có thể vượt quá giới hạn `int`, do đó các biến `num`, `product`, `sum` đều phải sử dụng kiểu `long long` và áp dụng phép `% MOD` ngay sau mỗi phép nhân hoặc cộng để tránh tràn số (Overflow).

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

const long long MOD = 1000000007;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    string s;
    cin >> s;

    long long sum = 0;
    long long product = 1;
    bool needNumber = true; // Cờ hiệu: true = đang cần đọc số, false = đang cần đọc toán tử

    for (int i = 0; i < s.length(); ) {
        if (needNumber) {
            // Vị trí này bắt buộc phải là chữ số đầu tiên của một toán hạng
            if (!isdigit(s[i])) {
                cout << "NOT_CORRECT\n";
                return 0;
            }
            long long num = 0;
            // Đọc trọn vẹn một số nguyên (có thể nhiều chữ số)
            while (i < s.length() && isdigit(s[i])) {
                num = (num * 10 + (s[i] - '0')) % MOD;
                i++;
            }
            // Nhân dồn vào tích hiện tại (độ ưu tiên của phép *)
            product = (product * num) % MOD;
            needNumber = false; // Chuyển sang trạng thái chờ toán tử
        } else {
            // Vị trí này bắt buộc phải là toán tử '+' hoặc '*'
            if (s[i] != '+' && s[i] != '*') {
                cout << "NOT_CORRECT\n";
                return 0;
            }
            if (s[i] == '+') {
                // Gặp dấu '+', chốt cụm nhân hiện tại cộng vào tổng chung
                sum = (sum + product) % MOD;
                product = 1;
            }
            i++;
            needNumber = true; // Chuyển sang trạng thái chờ số tiếp theo
        }
    }

    // Nếu kết thúc chuỗi mà vẫn đang chờ số (tức biểu thức kết thúc bằng dấu toán tử) -> Sai cú pháp
    if (needNumber) {
        cout << "NOT_CORRECT\n";
        return 0;
    }

    // Cộng nốt cụm tích cuối cùng vào tổng
    sum = (sum + product) % MOD;
    cout << sum << "\n";

    return 0;
}
```
---

## Bài 11: Phân tích số thành tổng các số nguyên dương (Integer Partitioning)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Quay lui vét cạn (**Backtracking**) kết hợp duy trì tính không giảm (`x[i] >= x[i-1]`).
* **Mục đích:** Liệt kê tất cả các cách phân tích số nguyên dương $N$ thành tổng của một hoặc nhiều số nguyên dương sao cho các cách phân tích là duy nhất (không tính thứ tự các số hạng).
* **Logic hoạt động:**
  1. Sử dụng mảng `x[]` để lưu cấu hình phân tích hiện tại và biến `sum` để theo dõi tổng các số đã chọn.
  2. **Tránh trùng lặp cấu hình (Mấu chốt):** Để tránh việc sinh ra các cấu hình hoán vị trùng nhau (ví dụ: `1 + 2` và `2 + 1`), ta bắt buộc số hạng sau phải lớn hơn hoặc bằng số hạng liền trước (`value >= x[i-1]`).
  3. Giá trị `value` được thử trong khoảng từ `start` (bằng `x[i-1]` hoặc `1` nếu ở vị trí đầu) đến `N - sum` (để tổng không vượt quá $N$).
  4. Điều kiện dừng: Khi `sum == N`, in ra cấu hình từ `x[1]` đến `x[i-1]`.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao biến `start` ở các bước sau lại gán bằng `x[i-1]` mà không phải luôn bắt đầu từ 1?"*
  * **Trả lời:** Việc bắt đầu từ `x[i-1]` đảm bảo dãy các số hạng được chọn luôn theo **thứ tự không giảm** ($x_1 \le x_2 \le \dots \le x_k$). Điều này loại bỏ hoàn toàn các trường hợp lặp hoán vị của cùng một tập số hạng, đảm bảo mỗi phân tích chỉ xuất hiện đúng 1 lần duy nhất theo thứ tự từ điển.
* **Thầy cô hỏi:** *"Tại sao điều kiện lặp lại là `value <= N - sum`?"*
  * **Trả lời:** Đây chính là một bước **nhánh cận đơn giản (Pruning)**. Vì tổng tối đa cho phép là $N$, nên số hạng tiếp theo `value` không bao giờ được vượt quá lượng còn thiếu là `N - sum`. Nếu `value > N - sum` thì tổng sẽ lập tức vượt quá $N$ và không thể tạo ra nghiệm hợp lệ.
* **Thầy cô hỏi:** *"Độ phức tạp không gian (Space Complexity) bài này là bao nhiêu?"*
  * **Trả lời:** Mảng `x[25]` lưu tối đa $N$ phần tử (trường hợp $1+1+\dots+1$), cùng với độ sâu đệ quy tối đa là $N$, nên độ phức tạp bộ nhớ là **$O(N)$** — cực kỳ tối ưu.

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int N;
int x[25]; // Mảng lưu các số hạng trong phân tích

// Hàm quay lui: i là vị trí số hạng đang xét, sum là tổng hiện tại
void Try(int i, int sum) {
    // Điều kiện dừng: Đã tổng đủ N
    if (sum == N) {
        for (int j = 1; j < i; j++) {
            cout << x[j] << (j < i - 1 ? " " : "");
        }
        cout << '\n';
        return;
    }

    // Đảm bảo dãy số hạng không giảm để tránh lặp nghiệm (1+2 trùng 2+1)
    int start = (i == 1) ? 1 : x[i - 1];

    // Cắt nhánh: Số hạng mới không được vượt quá phần còn thiếu (N - sum)
    for (int value = start; value <= N - sum; value++) {
        x[i] = value;
        Try(i + 1, sum + value); // Đệ quy xét vị trí tiếp theo
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> N;

    Try(1, 0); // Bắt đầu từ số hạng 1, tổng ban đầu = 0

    return 0;
}
```
---

## Bài 12: Đường đi của con mã có trọng số lớn nhất (Branch and Bound for Knight's Path)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Quay lui kết hợp Nhánh cận (**Backtracking with Branch and Bound**).
* **Mục đích:** Tìm một đường đi của quân Mã (Knight) trên bàn cờ kích thước $N \times N$ bắt đầu từ ô xuất phát `(r, c)` sao cho tổng trọng số các ô đi qua là lớn nhất có thể. Mỗi ô trên bàn cờ chỉ được đi tối đa một lần.
* **Logic hoạt động:**
  1. Quân Mã di chuyển theo 8 hướng chữ L thông qua hai mảng dịch chuyển `dr` và `dc`.
  2. Sử dụng mảng `visited[11][11]` để đánh dấu các ô đã đi qua.
  3. **Hàm đánh giá cận (`bound()`):** Tính tổng giá trị hiện tại (`sum`) cộng với tất cả các ô chưa được thăm có trọng số dương còn lại trên bàn cờ. 
  4. **Chiến lược nhánh cận (Pruning):** Nếu giá trị đánh giá tối đa có thể đạt được của nhánh này (`bound()`) mà nhỏ hơn hoặc bằng giá trị kỷ lục hiện tại (`ans`), ta lập tức cắt nhánh (`return`), không đi sâu tiếp vì chắc chắn không thể tìm ra kết quả tốt hơn.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Hàm `bound()` hoạt động theo nguyên lý gì và tại sao lại dùng nó để cắt nhánh?"*
  * **Trả lời:** Hàm `bound()` đóng vai trò là một **cận trên (Upper Bound)** lạc quan. Nó giả định rằng từ trạng thái hiện tại, con mã có thể vét sạch tất cả các ô chưa thăm còn lại có giá trị dương trên bàn cờ. Nếu ngay cả khi vét sạch lý tưởng như vậy mà tổng điểm vẫn không vượt qua được `ans` hiện tại (`bound() <= ans`), thì nhánh tìm kiếm này hoàn toàn vô vọng và bị loại bỏ sớm, giúp tiết kiệm lượng lớn thời gian tính toán.
* **Thầy cô hỏi:** *"Tại sao biến `sum` và mảng `visited` cần phải được phục hồi trạng thái (backtrack) sau khi gọi đệ quy `Try`?"*
  * **Trả lời:** Vì quân Mã cần phải thử nhiều hướng di chuyển khác nhau từ một ô để vét cạn tìm đường đi tối ưu nhất. Khi quay lui từ một ô con về, ta phải trừ đi trọng số `sum -= G[nx][ny]` và gỡ bỏ đánh dấu `visited[nx][ny] = false` để trả lại trạng thái ban đầu cho các nhánh tìm kiếm khác.
* **Thầy cô hỏi:** *"Mảng `dr` và `dc` tượng trưng cho cái gì?"*
  * **Trả lời:** Tượng trưng cho 8 bước dịch chuyển hợp lệ theo hình chữ L của quân Mã trong môn cờ vua (ví dụ: lên 2 trái 1, lên 1 phải 2, xuống 2 trái 1,...).

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

int N, r, c;
int G[11][11];
bool visited[11][11];

int ans = 0;
int sum = 0;

// 8 hướng di chuyển của quân Mã trong bàn cờ
int dr[8] = {-2, -2, -1, -1, 1, 1, 2, 2};
int dc[8] = {-1, 1, -2, 2, -2, 2, -1, 1};

// Hàm ước lượng cận trên (Upper Bound)
int bound() {
    int res = sum;
    for (int i = 1; i <= N; i++) {
        for (int j = 1; j <= N; j++) {
            // Cộng dồn tất cả các ô chưa thăm và có giá trị dương
            if (!visited[i][j] && G[i][j] > 0)
                res += G[i][j];
        }
    }
    return res;
}

// Hàm quay lui kết hợp Branch and Bound
void Try(int x, int y) {
    ans = max(ans, sum); // Cập nhật kỷ lục giá trị lớn nhất

    // Nhánh cận: Nếu cận trên không vượt được ans hiện tại thì cắt nhánh
    if (bound() <= ans)
        return;

    for (int k = 0; k < 8; k++) {
        int nx = x + dr[k];
        int ny = y + dc[k];
        
        // Kiểm tra điều kiện biên và ô chưa được thăm
        if (nx >= 1 && nx <= N && ny >= 1 && ny <= N && !visited[nx][ny]) {
            visited[nx][ny] = true;
            sum += G[nx][ny];

            Try(nx, ny); // Đệ quy di chuyển tiếp

            // Phục hồi trạng thái (Backtrack)
            sum -= G[nx][ny];
            visited[nx][ny] = false;
        }
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> N >> r >> c;

    for (int i = 1; i <= N; i++) {
        for (int j = 1; j <= N; j++) {
            cin >> G[i][j];
        }
    }

    // Khởi tạo điểm xuất phát ban đầu
    visited[r][c] = true;
    sum = G[r][c];

    Try(r, c);

    cout << ans << "\n";

    return 0;
}
```
