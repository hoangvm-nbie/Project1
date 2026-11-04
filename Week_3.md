---

## Bài 1: Cây tìm kiếm nhị phân - Thêm phần tử và Duyệt tiền thứ tự (BST)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Cấu trúc dữ liệu cây tìm kiếm nhị phân (**Binary Search Tree - BST**) kết hợp đệ quy.
* **Mục đích:** 
  1. Xây dựng cây tìm kiếm nhị phân động từ dòng lệnh đầu vào (`insert`), tuân thủ tính chất: Các nút bên trái nhỏ hơn nút gốc, các nút bên phải lớn hơn nút gốc.
  2. Duyệt cây theo thứ tự **Tiền thứ tự (Pre-order traversal: Gốc $\rightarrow$ Trái $\rightarrow$ Phải)** và in ra các giá trị.
* **Logic hoạt động:**
  * Cấu trúc `Node` lưu trữ giá trị `val` cùng hai con trỏ `left` và `right`, khởi tạo thông qua constructor.
  * Hàm `insert()` tìm vị trí thích hợp theo quy tắc cây BST: Nếu giá trị $k$ nhỏ hơn giá trị nút hiện tại thì đệ quy sang cây con trái, lớn hơn thì đệ quy sang cây con phải. Khi gặp `nullptr` thì tạo nút mới và trả về.
  * Đọc câu lệnh liên tục cho đến khi gặp dấu `#`, sau đó gọi hàm `preOrder()` để in kết quả duyệt cây.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao trong hàm `insert`, việc gán lại con trỏ `root->left = insert(...)` hoặc `root->right = insert(...)` lại quan trọng?"*
  * **Trả lời:** Khi cây rỗng (`root == nullptr`), hàm `insert` tạo ra một `new Node(k)` và trả về địa chỉ của nó. Câu lệnh gán `root->left = ...` giúp liên kết nút cha với nút con vừa được cấp phát bộ nhớ mới đó. Nếu thiếu phép gán, nút mới sẽ bị tạo ra đơn lẻ mà không được nối vào cấu trúc cây.
* **Thầy cô hỏi:** *"Độ phức tạp thời gian khi chèn một phần tử vào cây BST là bao nhiêu?"*
  * **Trả lời:** 
    * Trường hợp trung bình (cây cân đối): **$O(\log N)$**.
    * Trường hợp xấu nhất (cây bị suy biến thành dạng danh sách liên kết thẳng khi dữ liệu đầu vào đã có sẵn thứ tự tăng/giảm dần): **$O(N)$**.
* **Thầy cô hỏi:** *"Thứ tự duyệt Pre-order có đặc điểm gì so với các cách duyệt In-order hay Post-order?"*
  * **Trả lời:** Pre-order (Tiền thứ tự) xử lý nút gốc trước rồi mới duyệt đệ quy sang hai cây con trái và phải. Đối với cây BST, thứ tự In-order (Trung thứ tự: Trái $\rightarrow$ Gốc $\rightarrow$ Phải) sẽ luôn trả về một dãy số đã được sắp xếp tăng dần.

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

// Khai báo cấu trúc một Node trong cây
struct Node {
    int val;
    Node* left;
    Node* right;

    Node(int value) {
        val = value;
        left = nullptr;
        right = nullptr;
    }
};

// Hàm chèn giá trị k vào cây tìm kiếm nhị phân (BST)
Node* insert(Node* root, int k) {
    if (root == nullptr)
        return new Node(k); // Tạo nút mới nếu gặp ô trống
    
    if (k < root->val)
        root->left = insert(root->left, k);   // Nhỏ hơn thì rẽ trái
    else if (k > root->val)
        root->right = insert(root->right, k); // Lớn hơn thì rẽ phải
    
    return root; // Trả về con trỏ gốc (đã được cập nhật liên kết)
}

// Duyệt cây theo thứ tự Tiền thứ tự (Pre-order: Gốc -> Trái -> Phải)
void preOrder(Node* root) {
    if (root == nullptr) return;
    
    cout << root->val << " "; // Thăm gốc
    preOrder(root->left);     // Duyệt cây con trái
    preOrder(root->right);    // Duyệt cây con phải
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    Node* root = nullptr;
    string cmd;

    // Đọc các lệnh cho đến khi gặp dấu "#"
    while (cin >> cmd) {
        if (cmd == "#") {
            break;
        }
        if (cmd == "insert") {
            int k;
            cin >> k;
            root = insert(root, k);
        }
    }

    // In kết quả duyệt cây Pre-order
    preOrder(root);
    cout << "\n";

    return 0;
}
```
---

## Bài 14: Tính giá trị biểu thức có dấu ngoặc, phép cộng và phép nhân (Shunting-Yard / Two Stacks Algorithm)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Sử dụng hai ngăn xếp (**Two Stacks**: Một ngăn xếp lưu giá trị `values` và một ngăn xếp lưu toán tử/dấu ngoặc `op`), kết hợp hàm kiểm tra cú pháp khắt khe (`isValid`).
* **Mục đích:** Xử lý và tính toán giá trị biểu thức toán học có chứa các toán hạng, phép cộng `+`, phép nhân `*` và dấu ngoặc tròn `()`, đồng thời trả về kết quả theo modulo $10^9 + 7$ hoặc thông báo `NOT_CORRECT` nếu biểu thức sai cấu trúc cú pháp.
* **Logic hoạt động:**
  1. **Kiểm tra cú pháp (`isValid`):** Quét qua chuỗi để đảm bảo tính hợp lệ của ngoặc (số lượng ngoặc mở bằng ngoặc đóng, không có ngoặc rỗng sai vị trí), vị trí các toán tử không bị sát nhau hoặc nằm ở đầu/cuối biểu thức, và các ký tự đều hợp lệ.
  2. **Thuật toán đánh giá biểu thức (Dựa trên quy tắc ưu tiên toán tử):**
     * Nếu gặp **số**, đọc trọn vẹn giá trị, lấy modulo cho $10^9 + 7$ và đẩy vào ngăn xếp `values`.
     * Nếu gặp **dấu mở ngoặc `(`**, đẩy vào ngăn xếp `op`.
     * Nếu gặp **dấu đóng ngoặc `)`**, thực hiện rút gọn các phép toán trong ngoặc cho đến khi gặp dấu `(`.
     * Nếu gặp **toán tử (`+`, `*`)**, so sánh độ ưu tiên (`pre`) với đỉnh ngăn xếp `op`. Nếu toán tử ở đỉnh có độ ưu tiên lớn hơn hoặc bằng, tiến hành tính toán trước rồi mới đẩy toán tử hiện tại vào stack.
  3. Sau khi duyệt hết chuỗi, rút gọn nốt các phép toán còn lại trong ngăn xếp `op`. Kết quả cuối cùng nằm duy nhất ở đỉnh ngăn xếp `values`.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao cần phải có một hàm `isValid` riêng biệt để kiểm tra trước thay vì để stack tự báo lỗi?"*
  * **Trả lời:** Biểu thức đầu vào có thể chứa những lỗi cú pháp rất dị mà các thuật toán tính toán thông thường trên stack dễ bị kẹt, đọc sai bộ nhớ hoặc sinh ra ngoại lệ (Segmentation Fault / Underflow Stack). Hàm `isValid` đóng vai trò sàng lọc toàn bộ các trường hợp sai cú pháp ngay từ bước đầu (như `()`, `5++7`, `*5+3`, `5(3)`, v.v.) để đảm bảo tính an toàn tuyệt đối cho chương trình.
* **Thầy cô hỏi:** *"Độ ưu tiên của các toán tử được quy định như thế nào trong hàm `pre()`?"*
  * **Trả lời:** Phép cộng `+` có độ ưu tiên thấp hơn (trả về giá trị `1`), phép nhân `*` có độ ưu tiên cao hơn (trả về giá trị `2`). Nhờ quy tắc này, khi gặp toán tử có độ ưu tiên thấp hơn hoặc bằng toán tử ở đỉnh stack, stack sẽ tự động thực hiện tính toán cái có độ ưu tiên cao hơn trước, đảm bảo tính đúng đắn của thứ tự toán học.
* **Thầy cô hỏi:** *"Tại sao mỗi lần tính toán giá trị qua hàm `cal()`, các số hạng và kết quả đều phải áp dụng phép `% MOD` ($10^9 + 7$)?"*
  * **Trả lời:** Do giá trị của biểu thức (đặc biệt là khi có nhiều phép nhân dồn) có thể tăng lên cực kỳ nhanh, vượt xa giới hạn lưu trữ của kiểu `long long`. Việc chia lấy dư liên tục sau mỗi phép cộng và phép nhân giúp giữ cho giá trị luôn nằm trong tầm kiểm soát, không bị tràn số (Overflow).

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

const long long MOD = 1e9 + 7;

// Quy định độ ưu tiên của toán tử
int pre(char op) {
    if (op == '+') return 1;
    if (op == '*') return 2;
    return 0;
}

// Hàm tính toán kết quả nhị phân kèm modulo
long long cal(long long a, long long b, char op) {
    if (op == '+') return (a + b) % MOD;
    if (op == '*') return (a * b) % MOD;
    return 0;
}

// Hàm kiểm tra tính hợp lệ cú pháp toàn diện của biểu thức
bool isValid(const string &s) {
    int open = 0;
    char lastChar = ' ';
    for (size_t i = 0; i < s.length(); i++) {
        char c = s[i];
        if (isspace(c)) continue;
        
        if (c == '(') {
            open++;
            if (isdigit(lastChar) || lastChar == ')') return false;
        } else if (c == ')') {
            open--;
            if (open < 0) return false;
            if (lastChar == '+' || lastChar == '*' || lastChar == '(') return false;
        } else if (c == '+' || c == '*') {
            if (lastChar == ' ' || lastChar == '+' || lastChar == '*' || lastChar == '(') return false;
        } else if (isdigit(c)) {
            if (lastChar == ')') return false;
        } else {
            return false; // Ký tự lạ không hợp lệ
        }
        lastChar = c;
    }
    // Không được kết thúc bằng toán tử hoặc mở ngoặc thừa
    if (lastChar == '+' || lastChar == '*' || lastChar == '(' || lastChar == ' ') return false;
    return open == 0;
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    
    string s;
    if (!getline(cin, s)) {
        cout << "NOT_CORRECT\n";
        return 0;
    }
    
    // Kiểm tra cú pháp trước khi tính toán
    if (!isValid(s)) {
        cout << "NOT_CORRECT\n";
        return 0;
    }

    stack<long long> values; // Stack lưu toán hạng
    stack<char> op;          // Stack lưu toán tử và dấu ngoặc

    for (size_t i = 0; i < s.length(); i++) {
        if (isspace(s[i])) continue;
        
        if (isdigit(s[i])) {
            long long val = 0;
            while (i < s.length() && isdigit(s[i])) {
                val = (val * 10 + (s[i] - '0')) % MOD;
                i++;
            }
            values.push(val);
            i--;
        } else if (s[i] == '(') {
            op.push(s[i]);
        } else if (s[i] == ')') {
            // Rút gọn biểu thức trong ngoặc
            while (!op.empty() && op.top() != '(') {
                if (values.size() < 2) {
                    cout << "NOT_CORRECT\n";
                    return 0;
                }
                long long val2 = values.top(); values.pop();
                long long val1 = values.top(); values.pop();
                char currentOp = op.top(); op.pop();
                values.push(cal(val1, val2, currentOp));
            }
            if (!op.empty()) op.pop(); // Xóa dấu '(' ra khỏi stack
        } else if (s[i] == '+' || s[i] == '*') {
            // Xử lý độ ưu tiên toán tử
            while (!op.empty() && pre(op.top()) >= pre(s[i])) {
                if (values.size() < 2) {
                    cout << "NOT_CORRECT\n";
                    return 0;
                }
                long long val2 = values.top(); values.pop();
                long long val1 = values.top(); values.pop();
                char currentOp = op.top(); op.pop();
                values.push(cal(val1, val2, currentOp));
            }
            op.push(s[i]);
        }
    }

    // Rút gọn nốt các phép toán còn lại trong stack
    while (!op.empty()) {
        if (values.size() < 2) {
            cout << "NOT_CORRECT\n";
            return 0;
        }
        long long val2 = values.top(); values.pop();
        long long val1 = values.top(); values.pop();
        char currentOp = op.top(); op.pop();
        values.push(cal(val1, val2, currentOp));
    }

    // Kiểm tra kết quả cuối cùng hợp lệ
    if (values.size() == 1) {
        cout << values.top() << "\n";
    } else {
        cout << "NOT_CORRECT\n";
    }
    
    return 0;
}
```
---
---

## Bài 15: Cây gia phả - Tính số con cháu và số thế hệ (Family Tree / Tree Traversal)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Biểu diễn cây tổng quát bằng Danh sách kế (`std::map<string, vector<string>>`) kết hợp Duyệt theo chiều sâu (**DFS / Recursion**).
* **Mục đích:** 
  1. Xây dựng cây quan hệ cha - con từ danh sách cặp `(con, cha)` nhập vào.
  2. Truy vấn `descendants(u)`: Đếm tổng số con cháu (bao gồm cả con trực tiếp, cháu, chắt,...) của nút $u$.
  3. Truy vấn `generation(u)`: Tính độ dài nhánh con cháu dài nhất xuất phát từ nút $u$ (tương đương chiều cao / số thế hệ con cháu của $u$).
* **Logic hoạt động:**
  * Dùng `tree[p].push_back(c)` để lưu danh sách các con của người cha `p`.
  * **Hàm `descendants(u)`:** Với mỗi con $v$ của $u$, cộng thêm $1$ (chính con $v$) và gọi đệ quy `descendants(v)` để cộng toàn bộ con cháu của $v$.
  * **Hàm `generation(u)`:** Độ cao thế hệ tại $u$ là giá trị lớn nhất trong số các độ cao từ các con $v$ cộng thêm $1$ (`max(h, 1 + generation(v))`).

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao ở hàm `descendants` câu lệnh lại là `cnt += 1 + descendants(v)` còn hàm `generation` lại là `h = max(h, 1 + generation(v))`?"*
  * **Trả lời:** 
    * `descendants` đếm **tổng số nút** nằm trong cây con gốc $u$. Vì vậy ta phải thực hiện phép **cộng dồn** (`+=`) tất cả các nhánh con cháu lại với nhau.
    * `generation` tính **chiều cao (độ sâu tối đa)** của cây con gốc $u$. Do đó ta chỉ quan tâm tới nhánh kéo dài nhất, nên phải dùng hàm lấy giá trị **lớn nhất** (`max`).
* **Thầy cô hỏi:** *"Nếu một tên người `u` không có con cháu nào (nút lá hoặc không tồn tại trong cây) thì các hàm trả về kết quả gì?"*
  * **Trả lời:** Cả hai hàm sẽ không đi vào vòng lặp `for (string v : tree[u])` vì `tree[u]` rỗng, và đều trả về kết quả là `0` (0 con cháu và 0 thế hệ), hoàn toàn chính xác theo logic bài toán.
* **Thầy cô hỏi:** *"Độ phức tạp thời gian khi thực hiện mỗi truy vấn là bao nhiêu?"*
  * **Trả lời:** Với cây có $N$ nút, mỗi truy vấn chạy thuật toán DFS thăm lại các nút trong cây con gốc $u$ đúng 1 lần. Kết hợp với phép tra cứu `map` theo tên có độ dài $L$, độ phức tạp mỗi truy vấn là $O(N \cdot L \log N)$.

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

// Biểu diễn cây: Key là tên cha, Value là danh sách các con
map<string, vector<string>> tree;

// Hàm đếm tổng số con cháu của nút u (DFS)
int descendants(string u) {
    int cnt = 0;
    for (string v : tree[u]) {
        cnt += 1 + descendants(v); // 1 (con v) + số con cháu của v
    }
    return cnt;
}

// Hàm tính số thế hệ (chiều cao cây con) của nút u (DFS)
int generation(string u) {
    int h = 0;
    for (string v : tree[u]) {
        h = max(h, 1 + generation(v)); // Tìm nhánh dài nhất
    }
    return h;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    string c, p;
    // Đọc danh sách các cặp (con, cha) cho đến khi gặp "***"
    while (cin >> c && c != "***") {
        cin >> p;
        tree[p].push_back(c);
    }

    string cmd, name;
    // Xử lý các truy vấn cho đến khi gặp "***"
    while (cin >> cmd && cmd != "***") {
        cin >> name;

        if (cmd == "descendants")
            cout << descendants(name) << '\n';
        else if (cmd == "generation")
            cout << generation(name) << '\n';
    }

    return 0;
}
```
linked list
```
#include <bits/stdc++.h>
using namespace std;
struct Node {
    int data;
    Node* next;
    Node(int val) {
        data = val;
        next = nullptr;
    }
};
Node* findNode(Node* head, int k) {
    Node* cur = head;
    while (cur != nullptr) {
        if (cur->data == k) return cur;
        cur = cur->next;
    }
    return nullptr;
}

Node* addFirst(Node* head, int k) {
    if (findNode(head, k) != nullptr) return head;
    Node* newNode = new Node(k);
    newNode->next = head;
    return newNode;
}

Node* addLast(Node* head, int k) {
    if (findNode(head, k) != nullptr) return head;
    Node* newNode = new Node(k);
    if (head == nullptr) return newNode;

    Node* cur = head;
    while (cur->next != nullptr) {
        cur = cur->next;
    }
    cur->next = newNode;
    return head;
}

Node* addAfter(Node* head, int u, int v) {
    if (findNode(head, u) != nullptr) return head;
    Node* posV = findNode(head, v);
    if (posV == nullptr) return head;

    Node* newNode = new Node(u);
    newNode->next = posV->next;
    posV->next = newNode;
    return head;
}

Node* addBefore(Node* head, int u, int v) {
    if (findNode(head, u) != nullptr || findNode(head, v) == nullptr) return head;
    if (head->data == v) {
        return addFirst(head, u);
    }
    Node* prev = head;
    while (prev->next->data != v) {
        prev = prev->next;
    }
    Node* newNode = new Node(u);
    newNode->next = prev->next;
    prev->next = newNode;
    return head;
}

Node* removee(Node* head, int k) {
    if (findNode(head, k) == nullptr) return head;

    if (head->data == k) {
        Node* cur = head;
        head = head->next;
        delete cur;
        return head;
    }

    Node* prev = head;
    while (prev->next->data != k) {
        prev = prev->next;
    }

    Node* temp = prev->next;
    prev->next = prev->next->next;
    delete temp;

    return head;
}

Node* reversee(Node* head) {
    Node* prev = nullptr;
    Node* curr = head;
    Node* next = nullptr;

    while (curr != nullptr) {
        next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
    }

    return prev;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    Node* head = nullptr;
    int n;
    if (!(cin >> n)) return 0;
    for (int i = 0; i < n; ++i) {
        int x;
        cin >> x;
        head = addLast(head, x);
    }
    string type;
    while (cin >> type) {
        if (type == "#") {
            break;
        } else if (type == "addfirst") {
            int k;
            cin >> k;
            head = addFirst(head, k);
        } else if (type == "addlast") {
            int k;
            cin >> k;
            head = addLast(head, k);
        } else if (type == "addafter") {
            int u, v;
            cin >> u >> v;
            head = addAfter(head, u, v);
        } else if (type == "addbefore") {
            int u, v;
            cin >> u >> v;
            head = addBefore(head, u, v);
        } else if (type == "remove") {
            int k;
            cin >> k;
            head = removee(head, k);
        } else if (type == "reverse") {
            head = reversee(head);
        }
    }
    Node* cur = head;
    while (cur != nullptr) {
        cout << cur->data << " ";
        cur = cur->next;
    }
    cout << "\n";

    return 0;
}
```
---

## Bài 16: Cây tổng quát - Quản lý và Duyệt cây (PreOrder, InOrder, PostOrder)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Biểu diễn cây tổng quát bằng Cấu trúc con trỏ (`vector<Node*> children`) kết hợp `std::unordered_map<int, Node*>` để truy xuất nút theo `id` với độ phức tạp **$O(1)$**.
* **Mục đích:** 
  1. Quản lý việc tạo gốc (`MakeRoot`) và thêm nút con `u` vào nút cha `v` (`Insert`).
  2. Thực hiện 3 phép duyệt cây cơ bản trên cây n-ary (cây tổng quát): **PreOrder**, **InOrder**, và **PostOrder**.
* **Logic hoạt động của các phép duyệt:**
  * **PreOrder (Tiền thứ tự):** Thăm nút gốc `r` trước $\rightarrow$ Duyệt lần lượt tất cả các cây con từ trái sang phải.
  * **InOrder (Trung thứ tự trên cây n-ary):** Duyệt cây con đầu tiên (`children[0]`) $\rightarrow$ Thăm nút gốc `r` $\rightarrow$ Duyệt lần lượt các cây con còn lại từ `children[1]` đến hết. *(Nếu nút là lá thì in trực tiếp giá trị của nó)*.
  * **PostOrder (Hậu thứ tự):** Duyệt tất cả các cây con từ trái sang phải trước $\rightarrow$ Thăm nút gốc `r` cuối cùng.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Khái niệm Duyệt Trung thứ tự (InOrder) được định nghĩa như thế nào trên Cây tổng quát (n-ary tree) khi một nút có thể có nhiều hơn 2 con?"*
  * **Trả lời:** Với cây tổng quát, phép duyệt InOrder được quy ước: Duyệt đệ quy cây con thứ nhất (trái nhất) $\rightarrow$ Thăm nút gốc $\rightarrow$ Duyệt đệ quy tất cả các cây con còn lại từ cây con thứ hai đến cây con cuối cùng. Dòng code `inOrder(r->children[0])`, `cout << r->id`, và vòng lặp từ `i = 1` thể hiện chính xác quy tắc này.
* **Thầy cô hỏi:** *"Vai trò của mảng băm `unordered_map<int, Node*> nodes` trong bài này là gì?"*
  * **Trả lời:** `unordered_map` giúp lưu trữ và tra cứu con trỏ tới nút có `id` tương ứng trong thời gian trung bình $O(1)$. Nhờ đó, thao tác `insertNode(u, v)` (thêm nút `u` làm con của `v`) có thể thực hiện tức thì mà không cần phải chạy đệ quy tìm kiếm nút `v` trên toàn bộ cây, tối ưu thời gian dựng cây xuống $O(1)$ cho mỗi câu lệnh.
* **Thầy cô hỏi:** *"Hàm `insertNode` xử lý các ràng buộc hợp lệ như thế nào?"*
  * **Trả lời:** Câu lệnh `if (nodes.count(u) || !nodes.count(v)) return;` đảm bảo 2 điều kiện:
    1. Nút mới `u` **chưa từng tồn tại** trên cây (`!nodes.count(u)`).
    2. Nút cha `v` **bắt buộc phải đã tồn tại** trên cây (`nodes.count(v)`).
    Nếu vi phạm 1 trong 2 điều kiện này, thao tác `Insert` sẽ bị bỏ qua để tránh tạo vòng lặp hoặc ngắt kết nối cây.

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

// Cấu trúc một nút trên cây tổng quát
struct Node {
    int id;
    vector<Node*> children; // Danh sách con trỏ tới các nút con
    Node(int _id) : id(_id) {}
};

unordered_map<int, Node*> nodes; // Hash map quản lý nhanh các nút O(1)
Node* root = nullptr;

// Tạo nút gốc (chỉ tạo khi cây chưa có gốc)
void makeRoot(int u) {
    if (root != nullptr) return;
    root = new Node(u);
    nodes[u] = root;
}

// Thêm nút u làm con của nút v
void insertNode(int u, int v) {
    // Điều kiện: u chưa có trên cây VÀ v phải đã tồn tại trên cây
    if (nodes.count(u) || !nodes.count(v)) return;

    Node* child = new Node(u);
    nodes[u] = child;
    nodes[v]->children.push_back(child);
}

// Duyệt Tiền thứ tự: Gốc -> Các cây con (Trái qua Phải)
void preOrder(Node* r) {
    if (r == nullptr) return;
    cout << r->id << " ";
    for (Node* child : r->children) {
        preOrder(child);
    }
}

// Duyệt Trung thứ tự: Cây con 0 -> Gốc -> Các cây con còn lại (1..N-1)
void inOrder(Node* r) {
    if (r == nullptr) return;
    if (!r->children.empty()) {
        inOrder(r->children[0]); // Duyệt cây con đầu tiên
    }
    cout << r->id << " ";         // Thăm gốc
    for (size_t i = 1; i < r->children.size(); ++i) {
        inOrder(r->children[i]); // Duyệt các cây con từ thứ 2 trở đi
    }
}

// Duyệt Hậu thứ tự: Các cây con (Trái qua Phải) -> Gốc
void postOrder(Node* r) {
    if (r == nullptr) return;
    for (Node* child : r->children) {
        postOrder(child);
    }
    cout << r->id << " ";
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    string cmd;
    while (cin >> cmd && cmd != "*") {
        if (cmd == "MakeRoot") {
            int u; cin >> u;
            makeRoot(u);
        } else if (cmd == "Insert") {
            int u, v; cin >> u >> v;
            insertNode(u, v);
        } else if (cmd == "PreOrder") {
            preOrder(root);
            cout << "\n";
        } else if (cmd == "InOrder") {
            inOrder(root);
            cout << "\n";
        } else if (cmd == "PostOrder") {
            postOrder(root);
            cout << "\n";
        }
    }
    return 0;
}
```
---

## Bài 17: Bài toán đong nước hai bình (Water Jug Problem - BFS)

### 1. Ý tưởng cốt lõi & Cấu trúc dữ liệu
* **Kỹ thuật:** Tìm kiếm theo chiều rộng trên đồ thị trạng thái (**BFS - Breadth-First Search**).
* **Mục đích:** Tìm số bước đi ít nhất để đong được lượng nước đúng bằng `c` lít (nằm ở bình A, bình B, hoặc tổng lượng nước của cả hai bình), sử dụng hai bình có dung tích `a` và `b`.
* **Logic hoạt động:**
  1. Mỗi trạng thái của hệ thống được biểu diễn bằng cấu trúc `State { x, y, steps }` với `x` là lượng nước trong bình A, `y` là lượng nước trong bình B, và `steps` là số bước thực hiện tính từ trạng thái ban đầu `(0, 0)`.
  2. Từ trạng thái hiện tại `(cur.x, cur.y)`, sinh ra tối đa 6 hành động hợp lệ (các trạng thái tiếp theo):
     * Đổ đầy bình A: `(a, cur.y)`
     * Đổ đầy bình B: `(cur.x, b)`
     * Đổ cạn bình A: `(0, cur.y)`
     * Đổ cạn bình B: `(cur.x, 0)`
     * Đổ nước từ bình A sang bình B cho đến khi bình B đầy hoặc bình A cạn.
     * Đổ nước từ bình B sang bình A cho đến khi bình A đầy hoặc bình B cạn.
  3. Sử dụng mảng `visited[905][905]` để đánh dấu các cặp trạng thái `(x, y)` đã đi qua nhằm tránh lặp vô hạn.
  4. Vì BFS duyệt theo từng lớp độ dài tăng dần, lần đầu tiên chạm tới trạng thái thỏa mãn điều kiện (`cur.x == c || cur.y == c || cur.x + cur.y == c`) chắc chắn sẽ cho ra **số bước ít nhất**.

---

### 2. Điểm bẫy (Edge Cases) & Câu hỏi vấn đáp thường gặp
* **Thầy cô hỏi:** *"Tại sao bài toán tìm đường đi ngắn nhất này lại dùng BFS mà không dùng DFS (Quay lui)?"*
  * **Trả lời:** BFS có tính chất lan truyền theo từng tầng (từng bước đong), đảm bảo rằng trạng thái đạt được mục tiêu đầu tiên chính là trạng thái có số bước nhỏ nhất (đường đi ngắn nhất). Nếu dùng DFS, thuật toán có thể đi vào các nhánh vòng lặp vô hạn hoặc tìm ra một đường đi rất dài trước khi tìm thấy đường đi ngắn nhất.
* **Thầy cô hỏi:** *"Điều kiện `if (c > a + b)` ở đầu chương trình có ý nghĩa gì?"*
  * **Trả lời:** Đây là một điều kiện cắt nhanh (Pruning / Edge case check). Tổng lượng nước tối đa có thể chứa trong cả hai bình kết hợp lại chỉ là `a + b`. Nếu mục tiêu `c` lớn hơn sức chứa tối đa này, việc đong nước là bất khả thi, lập tức trả về `-1` mà không cần chạy thuật toán BFS tốn thời gian.
* **Thầy cô hỏi:** *"Công thức tính lượng nước khi đổ từ bình A sang bình B (`pour_to_b = min(cur.x, b - cur.y)`) hoạt động như thế nào?"*
  * **Trả lời:** Lượng nước có thể đổ sang bình B bị giới hạn bởi hai yếu tố: (1) Toàn bộ lượng nước hiện có trong bình A (`cur.x`), và (2) Khoảng trống còn lại trong bình B (`b - cur.y`). Lấy giá trị nhỏ nhất giữa hai đại lượng này chính là lượng nước thực tế được chuyển dịch.

---

### 3. Đoạn Code Cốt Lõi (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

// Cấu trúc lưu trữ trạng thái của hai bình nước
struct State {
    int x;     // Lượng nước trong bình A
    int y;     // Lượng nước trong bình B
    int steps; // Số bước đã thực hiện
};

// Mảng đánh dấu các trạng thái đã duyệt qua (giả định dung tích bình <= 900)
bool visited[905][905];

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int a, b, c;
    if (!(cin >> a >> b >> c)) return 0;
    
    // Nếu lượng nước cần đong lớn hơn tổng dung tích 2 bình -> không có lời giải
    if (c > a + b) {
        cout << -1 << "\n";
        return 0;
    }

    queue<State> q;
    q.push({0, 0, 0}); // Trạng thái ban đầu: cả 2 bình trống, 0 bước
    visited[0][0] = true;

    while (!q.empty()) {
        State cur = q.front();
        q.pop();

        // Kiểm tra điều kiện đạt mục tiêu c
        if (cur.x == c || cur.y == c || cur.x + cur.y == c) {
            cout << cur.steps << "\n";
            return 0;
        }

        vector<pair<int, int>> next_states;
        // 1. Đổ đầy bình A
        next_states.push_back({a, cur.y});
        // 2. Đổ đầy bình B
        next_states.push_back({cur.x, b});
        // 3. Đổ cạn bình A
        next_states.push_back({0, cur.y});
        // 4. Đổ cạn bình B
        next_states.push_back({cur.x, 0});
        
        // 5. Đổ từ A sang B
        int pour_to_b = min(cur.x, b - cur.y);
        next_states.push_back({cur.x - pour_to_b, cur.y + pour_to_b});
        
        // 6. Đổ từ B sang A
        int pour_to_a = min(cur.y, a - cur.x);
        next_states.push_back({cur.x + pour_to_a, cur.y - pour_to_a});

        // Duyệt qua các trạng thái tiếp theo
        for (auto& p : next_states) {
            int nx = p.first;
            int ny = p.second;

            if (!visited[nx][ny]) {
                visited[nx][ny] = true;
                q.push({nx, ny, cur.steps + 1});
            }
        }
    }

    // Không tìm thấy cách đong
    cout << -1 << "\n";
    return 0;
}
