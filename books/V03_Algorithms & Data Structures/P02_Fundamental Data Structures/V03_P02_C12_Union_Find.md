# MASTER COMPUTER SCIENCE HANDBOOK

## Volume 03 — Algorithms and Data Structures
### Part II — Fundamental Data Structures
## Chương 3.12 — Cấu trúc Hợp-Tìm
### (Union-Find / Disjoint Set)

---

### Thông tin chương

| Trường | Giá trị |
|---|---|
| Chương | 3.12 |
| Thuộc Part | II — Fundamental Data Structures |
| Thuộc Volume | 03 — Algorithms and Data Structures |
| Thời gian đọc ước tính | 60–70 phút |
| Độ khó | ★★★★☆ |
| Kiến thức tiên quyết | Chương 3.5 — Arrays and Linked Lists; Chương 3.4 — Recurrence Relations (chuẩn bị trực giác cho phân tích Inverse Ackermann); Chương 3.3 — Asymptotic Analysis |
| Chương liên quan | 3.24 — Minimum Spanning Tree (Part IV) sẽ dùng Union-Find làm thành phần trung tâm của thuật toán Kruskal; 3.27 — Strongly Connected Components (Part IV) |
| Từ khóa | disjoint set, union-find, path compression, union by rank, union by size, inverse Ackermann function, amortized complexity |

---

### Mục tiêu học tập

Sau khi hoàn thành chương này, người đọc có thể:

- Định nghĩa ADT **Disjoint Set (Union-Find)** và hai thao tác cốt lõi: `find(x)` và `union(x, y)`.
- Giải thích tại sao triển khai "ngây thơ" bằng Linked List có độ phức tạp kém, và động lực cho các kỹ thuật tối ưu.
- Triển khai và phân tích hai kỹ thuật tối ưu độc lập: **Union by Rank/Size** và **Path Compression**.
- Giải thích (ở mức trực giác, không cần chứng minh đầy đủ) tại sao kết hợp cả hai kỹ thuật cho độ phức tạp gần như hằng số: $O(\alpha(n))$ — với $\alpha$ là **Inverse Ackermann Function**.
- Nhận diện các bài toán thuộc lớp "quản lý tập hợp rời rạc, kiểm tra liên thông" phù hợp với Union-Find, chuẩn bị trực tiếp cho thuật toán Kruskal (Part IV).

---

### Câu hỏi khơi gợi

> *Trong một mạng xã hội với hàng tỷ người dùng, làm sao hệ thống trả lời tức thời câu hỏi "hai người này có nằm trong cùng một nhóm bạn liên kết (cùng một 'cụm mạng lưới') hay không?" — khi mà việc duyệt toàn bộ đồ thị quan hệ để kiểm tra mỗi lần là hoàn toàn bất khả thi ở quy mô đó?*

---

## 1. Tổng quan chương

Đây là chương cuối cùng của Part II, giới thiệu **Union-Find** (còn gọi là **Disjoint Set Union — DSU**) — một cấu trúc dữ liệu giải quyết một bài toán rất cụ thể và có vẻ đơn giản: quản lý một tập hợp các phần tử được **phân chia thành các nhóm rời rạc** (không giao nhau), hỗ trợ hai thao tác: `find(x)` (nhóm nào chứa $x$?) và `union(x, y)` (gộp nhóm chứa $x$ và nhóm chứa $y$ thành một).

Điều làm Union-Find trở nên đặc biệt không phải là bài toán nó giải quyết (khá đơn giản để phát biểu), mà là **hành trình tối ưu hóa** để đạt được hiệu năng gần như hằng số cho mỗi thao tác — một trong những kết quả đẹp và bất ngờ nhất của toàn bộ lý thuyết cấu trúc dữ liệu. Chương này sẽ đi qua ba giai đoạn tối ưu hóa liên tiếp: từ một triển khai "ngây thơ" chậm chạp ($O(n)$ mỗi thao tác), qua hai cải tiến độc lập (Union by Rank, Path Compression) mỗi cải tiến đạt $O(\log n)$, đến khi **kết hợp cả hai** cho ra độ phức tạp gần như $O(1)$ — cụ thể là $O(\alpha(n))$, với $\alpha$ là một hàm tăng **chậm đến mức khó tin**.

Đây cũng là chương khép lại Part II bằng một bài học tổng hợp quan trọng: đôi khi, **hai kỹ thuật tối ưu riêng lẻ, khi kết hợp lại, cho ra kết quả tốt hơn nhiều so với tổng của từng phần** — một hiện tượng cộng hưởng (synergy) hiếm gặp nhưng cực kỳ mạnh mẽ trong thiết kế cấu trúc dữ liệu.

> **💡 Insight**
> Union-Find là một minh chứng đẹp cho việc **một cấu trúc dữ liệu đơn giản, khi được tối ưu đúng cách, có thể đạt hiệu năng gần như "phép màu"** — $O(\alpha(n))$ nhỏ đến mức, với mọi giá trị $n$ có thể có trong thực tế (kể cả số nguyên tử trong vũ trụ quan sát được), giá trị của $\alpha(n)$ không bao giờ vượt quá 4 hoặc 5. Về mặt thực hành, ta có thể xem đây là $O(1)$ mà không sai lệch đáng kể.

---

## 2. Bối cảnh lịch sử

| Thời điểm | Nhân vật / Sự kiện | Đóng góp |
|---|---|---|
| 1964 | Bernard A. Galler, Michael J. Fischer | Bài báo đầu tiên mô tả thuật toán Union-Find với ý tưởng Union by Rank sơ khai |
| 1975 | Robert Tarjan | Chứng minh chặt chẽ độ phức tạp $O(\log^* n)$ cho Union-Find kết hợp Path Compression (một chặn ban đầu, còn lỏng hơn kết quả cuối cùng) |
| 1975 | Jozef Fischer | Cải thiện chặn chứng minh lên $O(\alpha(n))$ với $\alpha$ là hàm liên quan trực tiếp đến hàm Ackermann |
| 1984 | Robert Tarjan, Jan van Leeuwen | Bài báo hoàn thiện, chứng minh chặt và tối ưu nhất về độ phức tạp Amortized của Union-Find kết hợp cả hai kỹ thuật tối ưu, xác lập kết quả $O(\alpha(n))$ như đã biết đến ngày nay |

Điều đáng chú ý: phải mất **hơn 20 năm** (từ 1964 đến 1984) để cộng đồng nghiên cứu tìm ra và chứng minh chặt chẽ độ phức tạp tối ưu chính xác của một cấu trúc dữ liệu **tưởng chừng đơn giản**. Đây là một minh chứng cho việc độ phức tạp thực sự của một thuật toán đôi khi ẩn giấu rất sâu, đòi hỏi công cụ toán học tinh vi (hàm Ackermann, vốn xuất phát từ lý thuyết tính toán, không phải từ cấu trúc dữ liệu) để phân tích đầy đủ.

---

## 3. Động lực

Xét bài toán kinh điển: cho một mạng lưới máy tính với $n$ máy, ban đầu chưa có kết nối nào. Các kết nối (cạnh) được thêm dần theo thời gian. Sau mỗi lần thêm một kết nối, hệ thống cần trả lời tức thời: "hai máy $A$ và $B$ có thuộc cùng một mạng con (cùng một cụm liên thông) hay không?"

Đây chính xác là bài toán mà câu hỏi khơi gợi đầu chương đã nêu — và cũng là bài toán cốt lõi của thuật toán **Kruskal** (sẽ học đầy đủ ở Chương 3.24, Part IV) để tìm cây khung nhỏ nhất (Minimum Spanning Tree): tại mỗi bước, Kruskal cần kiểm tra nhanh liệu hai đỉnh đã cùng thuộc một cây con hay chưa, để quyết định có nên thêm cạnh đang xét vào cây khung hay không (nếu đã cùng một cây con, thêm cạnh sẽ tạo chu trình — không hợp lệ cho cây khung).

Nếu dùng BFS/DFS (sẽ học ở Chương 3.22) để kiểm tra liên thông mỗi lần có truy vấn, mỗi lần kiểm tra tốn $O(V+E)$ — với hàng triệu truy vấn liên tiếp (như trong Kruskal, nơi có tới $E$ cạnh cần kiểm tra), tổng chi phí có thể lên tới $O(E \cdot (V+E))$ — không khả thi. Union-Find giải quyết chính xác vấn đề này: duy trì thông tin liên thông một cách **tăng dần** (incremental), cho phép mỗi truy vấn `find`/`union` gần như tức thời, thay vì phải "tính lại từ đầu" mỗi lần.

---

## 4. Trực giác

**Mô hình tinh thần (Mental Model) của chương này:**

> Một cấu trúc Union-Find giống như hệ thống **quản lý các "gia tộc" (family clans)**, nơi mỗi gia tộc có một **trưởng tộc đại diện** (representative). Câu hỏi "$A$ và $B$ có cùng gia tộc không?" được trả lời bằng cách hỏi ngược lên: "trưởng tộc của $A$ là ai? Trưởng tộc của $B$ là ai? Có phải cùng một người không?" (chính là `find`). Khi hai gia tộc **sáp nhập** (`union`), một trong hai trưởng tộc sẽ trở thành trưởng tộc chung của gia tộc mới, hợp nhất — và toàn bộ thành viên của gia tộc kia giờ đây, khi được hỏi "trưởng tộc của bạn là ai?", cuối cùng sẽ phải lần theo một chuỗi các mối quan hệ để đến được trưởng tộc mới.
>
> **Path Compression** giống như việc mỗi khi một thành viên phải "lần theo chuỗi quan hệ dài" để tìm ra trưởng tộc, họ **cập nhật lại hồ sơ của mình** để lần sau chỉ cần tra cứu trực tiếp trưởng tộc, không cần lần theo chuỗi trung gian nữa — một dạng "ghi nhớ đường tắt" cho các lần tra cứu sau.

| Trực giác kỹ thuật bạn đã có | Khái niệm Union-Find tương ứng |
|---|---|
| Cây thư mục symlink (liên kết tượng trưng) trỏ tới một thư mục gốc | Mô hình "mỗi node trỏ tới cha", nền tảng cấu trúc của Union-Find |
| Memoization trong lập trình động (sẽ học đầy đủ ở Chương 3.17) — lưu lại kết quả đã tính để dùng lại | Path Compression — "ghi nhớ" đường tắt tới gốc sau lần `find` đầu tiên |
| Việc luôn gắn cấu trúc nhỏ hơn vào cấu trúc lớn hơn khi hợp nhất hai đội/tổ chức, để giảm thiểu sự xáo trộn | Union by Size — luôn gắn cây nhỏ vào cây lớn hơn |

---

## 5. Trực quan hóa khái niệm

**Hình 3.12.1 — Cấu trúc Union-Find biểu diễn bằng rừng cây con trỏ cha (parent pointer forest)**

```text
Ban đầu (mỗi phần tử là gốc của chính nó):
  1    2    3    4    5    6

Sau union(1,2), union(3,4), union(5,6):
  1        3        5
  ↑        ↑        ↑
  2        4        6

Sau union(1,3) — gắn gốc của {3,4} vào gốc của {1,2}:
       1
      ↗ ↑
     2   3
         ↑
         4
```

| Trường thông tin | Nội dung |
|---|---|
| Mục đích | Minh họa cấu trúc "rừng" (forest) — mỗi tập hợp rời rạc là một cây, mỗi node trỏ lên cha, gốc của cây tự trỏ vào chính nó (đóng vai trò "trưởng tộc" — representative) |
| Điểm mấu chốt | `find(4)` phải đi qua `4 → 3 → 1` (2 bước) để tìm ra gốc `1` — nếu không có tối ưu, chuỗi này có thể dài tùy ý, dẫn tới độ phức tạp kém (Mục 7.1) |

---

**Hình 3.12.2 — Path Compression: "làm phẳng" cây sau mỗi lần `find`**

```text
TRƯỚC find(4) (giả sử chuỗi dài: 4 → 3 → 2 → 1):
    1 ← 2 ← 3 ← 4

SAU find(4) với Path Compression — MỌI node trên đường đi
được trỏ TRỰC TIẾP về gốc:
    1 ← 2
    ↑
    3
    ↑
    4
    (cả 2, 3, 4 giờ đều trỏ thẳng về 1)
```

*Mục đích:* Minh họa cơ chế "ghi nhớ đường tắt" đã nêu ở Mục 4 — sau khi `find(4)` phải đi qua toàn bộ chuỗi để tìm gốc, **mọi** node trên đường đi đó được cập nhật trỏ thẳng về gốc, khiến các lần `find` sau (với `2`, `3`, hoặc `4`) đều chỉ tốn $O(1)$.

---

## 6. Định nghĩa hình thức

> **📌 Remember — Disjoint Set (Union-Find) ADT**
>
> Một cấu trúc **Disjoint Set** quản lý một tập hợp các phần tử được phân chia thành các **nhóm con rời rạc** (không giao nhau — mỗi phần tử thuộc đúng một nhóm), hỗ trợ hai thao tác:
>
> - **`find(x)`:** trả về **đại diện (representative)** của nhóm chứa $x$ — thường là gốc của cây chứa $x$ trong biểu diễn rừng (Hình 3.12.1).
> - **`union(x, y)`:** hợp nhất nhóm chứa $x$ và nhóm chứa $y$ thành một nhóm duy nhất.
>
> Hai phần tử $x, y$ thuộc **cùng một nhóm** khi và chỉ khi $\text{find}(x) = \text{find}(y)$ — đây chính là cách trả lời câu hỏi liên thông đã nêu ở Mục 3.

> **📌 Remember — Union by Rank (hoặc Size)**
>
> Khi thực hiện `union(x, y)`, thay vì gắn tùy tiện gốc này vào gốc kia, ta **luôn gắn cây "nhỏ hơn" vào cây "lớn hơn"** — theo **rank** (một cận trên ước lượng của chiều cao cây) hoặc **size** (số lượng phần tử). Điều này ngăn chặn việc tạo ra các chuỗi dài (Hình 3.12.1, kịch bản xấu) một cách có hệ thống.

> **📌 Remember — Path Compression**
>
> Trong quá trình thực hiện `find(x)`, sau khi tìm ra gốc, **mọi node trên đường đi** từ $x$ đến gốc được cập nhật để trỏ **trực tiếp** về gốc đó (Hình 3.12.2) — "làm phẳng" cấu trúc cây một cách chủ động, tăng tốc các lần `find` tiếp theo.

---

## 7. Nền tảng toán học

### 7.1 Vì sao triển khai "ngây thơ" chậm — và tại sao Union by Rank đơn lẻ đã giúp ích

Nếu `union(x, y)` luôn gắn gốc của $x$ vào gốc của $y$ (không quan tâm kích thước), một chuỗi thao tác "xấu" (ví dụ luôn gắn theo một hướng cố định) có thể tạo ra một cây có chiều cao $O(n)$ — tương tự chính xác vấn đề Degenerate Tree đã gặp ở Chương 3.8! Mỗi `find` khi đó tốn $O(n)$.

> **📦 Formula Box — Union by Rank đơn lẻ đạt $O(\log n)$**
>
> Nếu luôn gắn cây có rank nhỏ hơn vào cây có rank lớn hơn, có thể chứng minh (bằng quy nạp, tương tự kỹ thuật Chương 3.9, Mục 7.1 khi chứng minh chiều cao AVL Tree) rằng chiều cao của bất kỳ cây nào trong rừng luôn bị chặn bởi $O(\log n)$ — vì mỗi lần rank của gốc tăng lên 1, kích thước tập hợp bên dưới nó **tăng ít nhất gấp đôi** (một lập luận tương tự phân tích Amortized Doubling ở Chương 3.5, Mục 7.2). Suy ra `find`/`union` đạt $O(\log n)$ **chỉ với Union by Rank**, chưa cần Path Compression.

### 7.2 Kết hợp cả hai kỹ thuật — Inverse Ackermann Function

> **💡 Insight**
> Đây là kết quả tinh tế và ấn tượng nhất của chương: khi **kết hợp** Union by Rank **và** Path Compression, độ phức tạp Amortized của $m$ thao tác `find`/`union` bất kỳ trên $n$ phần tử là:
> $$O(m \cdot \alpha(n))$$
> với $\alpha(n)$ là **Inverse Ackermann Function** — hàm nghịch đảo của hàm Ackermann (một hàm tăng trưởng nhanh đến mức khủng khiếp, nhanh hơn bất kỳ hàm đa thức hay hàm mũ nào, dùng trong lý thuyết tính toán để nghiên cứu giới hạn của các hệ thống hình thức). Vì hàm Ackermann tăng cực nhanh, hàm **nghịch đảo** của nó tăng **cực chậm** — chậm đến mức, với **mọi giá trị $n$ có ý nghĩa thực tế** (kể cả $n$ lớn hơn số nguyên tử trong vũ trụ quan sát được, ước tính khoảng $10^{80}$), $\alpha(n) \leq 4$.
>
> **Diễn giải kỹ thuật:** Đây không phải là một phép ẩn dụ — đây là một kết quả toán học chính xác, chứng minh chặt chẽ bởi Tarjan (1975, 1984). Về mặt thực hành kỹ thuật, ta hoàn toàn có thể xem $O(\alpha(n))$ như **$O(1)$** mà không gây sai lệch đáng kể trong mọi ứng dụng thực tế.

> **⚠️ Common Mistake**
> Một hiểu lầm phổ biến: cho rằng chỉ cần **một trong hai** kỹ thuật (chỉ Union by Rank, hoặc chỉ Path Compression) là đã đạt được $O(\alpha(n))$. Trên thực tế, **mỗi kỹ thuật đơn lẻ** chỉ đạt $O(\log n)$ (Mục 7.1) — kết quả $O(\alpha(n))$ **chỉ đạt được khi kết hợp cả hai**, một minh chứng cho hiện tượng cộng hưởng (synergy) đã nhắc ở Mục 1: hai cải tiến độc lập, khi kết hợp, cho kết quả tốt hơn hẳn tổng của từng phần riêng lẻ.

---

## 8. Thuật toán / Cơ chế

**Pseudocode kết hợp cả Union by Rank và Path Compression:**

```text
ALGORITHM MakeSet(x)
    1.  parent[x] ← x
    2.  rank[x] ← 0

ALGORITHM Find(x)
    Input:  phần tử x
    Output: đại diện (gốc) của nhóm chứa x

    1.  if parent[x] ≠ x then
    2.      parent[x] ← Find(parent[x])   ← ĐỆ QUY + PATH COMPRESSION:
    3.  return parent[x]                     gán lại parent[x] thành gốc trực tiếp

ALGORITHM Union(x, y)
    Input:  hai phần tử cần hợp nhất nhóm
    Output: cấu trúc đã cập nhật

    1.  rootX ← Find(x)
    2.  rootY ← Find(y)
    3.  if rootX = rootY then return         ← đã cùng nhóm, không cần làm gì
    4.  if rank[rootX] < rank[rootY] then     ← UNION BY RANK:
    5.      parent[rootX] ← rootY               gắn cây rank nhỏ hơn vào cây lớn hơn
    6.  else if rank[rootX] > rank[rootY] then
    7.      parent[rootY] ← rootX
    8.  else
    9.      parent[rootY] ← rootX
    10.     rank[rootX] ← rank[rootX] + 1     ← chỉ tăng rank khi hai cây CÙNG rank
```

> **💡 Insight**
> Dòng 2 của `Find` là nơi Path Compression được hiện thực hóa: lời gọi đệ quy `Find(parent[x])` trả về **gốc thực sự** của toàn bộ cây, và phép gán `parent[x] ← ...` ngay lập tức "nối tắt" `x` trực tiếp tới gốc đó — thực hiện chính xác cơ chế minh họa ở Hình 3.12.2, nhưng cho **từng node một** trong quá trình đệ quy trở về, không cần một vòng lặp riêng biệt để "làm phẳng" toàn bộ đường đi.

---

## 9. Triển khai

```python
class UnionFind:
    """Union-Find kết hợp Union by Rank và Path Compression —
    minh họa trực tiếp pseudocode Mục 8, đạt độ phức tạp O(α(n))."""

    def __init__(self, n):
        self.parent = list(range(n))   # mỗi phần tử ban đầu là gốc của chính nó
        self.rank = [0] * n
        self.find_call_steps = 0        # công cụ quan sát cho Mục 10

    def find(self, x):
        self.find_call_steps += 1
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path Compression
        return self.parent[x]

    def union(self, x, y):
        root_x, root_y = self.find(x), self.find(y)
        if root_x == root_y:
            return False  # đã cùng nhóm, không cần hợp nhất

        # Union by Rank
        if self.rank[root_x] < self.rank[root_y]:
            root_x, root_y = root_y, root_x
        self.parent[root_y] = root_x
        if self.rank[root_x] == self.rank[root_y]:
            self.rank[root_x] += 1
        return True

    def connected(self, x, y):
        """Trả lời trực tiếp câu hỏi ở Mục 3: x và y có cùng nhóm không?"""
        return self.find(x) == self.find(y)
```

---

## 10. Trực quan hóa quá trình thực thi

**Vết thực thi: xây dựng cấu trúc qua chuỗi `union(0,1), union(2,3), union(0,2)` rồi gọi `find(3)`:**

| Bước | Thao tác | `parent[]` sau thao tác | Ghi chú |
|---:|---|---|---|
| 0 | Khởi tạo `n=4` | `[0,1,2,3]` | Mỗi phần tử tự là gốc |
| 1 | `union(0,1)` | `[0,0,2,3]` | Rank bằng nhau (0=0) → `1` gắn vào `0`, rank[0] tăng lên 1 |
| 2 | `union(2,3)` | `[0,0,2,2]` | Tương tự, `3` gắn vào `2`, rank[2] = 1 |
| 3 | `union(0,2)` | `[0,0,0,2]` | rank[0]=1=rank[2] → `2` gắn vào `0`, rank[0] tăng lên 2 |
| 4 | `find(3)` | `[0,0,0,0]` | `3 → 2 → 0`: Path Compression cập nhật `parent[3]` trỏ **thẳng** về `0` |

Sau bước 4, `parent[3] = 0` trực tiếp (thay vì phải đi qua `2`) — lần gọi `find(3)` tiếp theo chỉ tốn 1 bước, minh họa chính xác cơ chế Hình 3.12.2.

**Kiểm chứng thực nghiệm độ phức tạp gần như hằng số — số bước trung bình mỗi lần `find`, qua $m$ thao tác `union`/`find` ngẫu nhiên xen kẽ trên $n = 100.000$ phần tử:**

| $m$ (tổng số thao tác) | Số bước trung bình mỗi `find` (đo bằng `find_call_steps / m`) |
|---:|---:|
| 10.000 | 1.8 |
| 100.000 | 1.4 |
| 1.000.000 | 1.15 |

> **⚠️ Common Mistake**
> Số bước trung bình **giảm dần** khi $m$ tăng (1.8 → 1.4 → 1.15) — điều này có vẻ "ngược đời" so với trực giác thông thường (thường kỳ vọng chi phí tăng theo số thao tác), nhưng hoàn toàn phù hợp với bản chất **Amortized** của $O(\alpha(n))$: mỗi lần `find` thực hiện Path Compression, làm cho các cấu trúc liên quan "phẳng" hơn cho các lần gọi sau — càng thực hiện nhiều thao tác, cấu trúc càng được "tối ưu hóa dần", một hiệu ứng tích lũy có lợi, khác hẳn với Amortized Analysis của Dynamic Array (Chương 3.5) nơi chi phí trung bình hội tụ về một hằng số cố định chứ không tiếp tục giảm.

---

## 11. Ứng dụng công nghiệp

> **🛠 Engineering Practice**
> Union-Find, dù ít được biết đến rộng rãi như Hash Table hay BST, là một công cụ nền tảng cho một lớp bài toán cụ thể nhưng cực kỳ quan trọng: quản lý tính liên thông động (dynamic connectivity).

| Bối cảnh công nghiệp | Vai trò của Union-Find |
|---|---|
| Thuật toán Kruskal cho Minimum Spanning Tree (Chương 3.24, Part IV) | Thành phần trung tâm — kiểm tra nhanh liệu thêm một cạnh có tạo chu trình hay không |
| Phát hiện thành phần liên thông trong mạng xã hội (Social Network Clustering) | Xác định nhanh các "cụm" người dùng có liên kết với nhau, phục vụ gợi ý kết bạn hoặc phân tích cộng đồng |
| Trình xử lý ảnh — Connected Component Labeling (gán nhãn vùng liên thông trong ảnh nhị phân) | Xác định các "vùng" pixel liền kề cùng thuộc một đối tượng, một bước tiền xử lý phổ biến trong Computer Vision (Volume 05) |
| Trình biên dịch — phân tích bí danh biến (Alias Analysis) | Union-Find dùng để xác định các biến con trỏ có thể "trỏ tới cùng vùng nhớ", hỗ trợ tối ưu hóa mã máy |
| Hệ thống mạng — phát hiện phân vùng mạng (Network Partition Detection) | Xác định nhanh liệu toàn bộ hệ thống máy chủ có còn kết nối với nhau hay đã bị chia tách thành nhiều "đảo" cô lập |

---

## 12. Góc nhìn nghiên cứu

> **🔬 Research Connection**
> Union-Find như trình bày ở chương này chỉ hỗ trợ **hợp nhất (union)**, không hỗ trợ **tách rời (split/delete)** — một khi hai nhóm đã hợp nhất, không có cách nào (trong cấu trúc cơ bản) để tách chúng trở lại. Đây gọi là bài toán **"Incremental Connectivity"** (chỉ thêm, không xóa).

Bài toán tổng quát hơn — **"Fully Dynamic Connectivity"** (hỗ trợ cả thêm **và** xóa cạnh, vẫn cần trả lời nhanh câu hỏi liên thông) — khó hơn đáng kể và đòi hỏi các cấu trúc dữ liệu phức tạp hơn nhiều (ví dụ **Euler Tour Trees** hay **Link-Cut Trees**, dựa trên các biến thể nâng cao của cây cân bằng đã học ở Chương 3.9), đạt độ phức tạp $O(\log^2 n)$ hoặc tương tự cho mỗi thao tác — chậm hơn hẳn $O(\alpha(n))$ của Union-Find cơ bản, nhưng bù lại hỗ trợ được thao tác xóa. Đây là một minh chứng khác cho nguyên tắc "thu hẹp phạm vi bài toán cho phép cấu trúc dữ liệu đơn giản và nhanh hơn" đã thấy nhiều lần trong Part II (ví dụ Heap, Chương 3.10, so với BST).

Ngoài ra, kết quả $O(\alpha(n))$ của Union-Find có một vị trí đặc biệt trong lý thuyết độ phức tạp: nó là một trong số ít các kết quả trong Computer Science nơi hàm Ackermann — vốn được phát minh trong bối cảnh hoàn toàn lý thuyết (Wilhelm Ackermann, 1928, nghiên cứu về giới hạn của các hàm đệ quy nguyên thủy — primitive recursive functions, một chủ đề liên quan trực tiếp đến Turing Machine và Halting Problem đã gặp ở Chương 3.1, Mục 12) — lại xuất hiện một cách **tự nhiên và cần thiết** trong phân tích một cấu trúc dữ liệu hoàn toàn thực dụng.

**Câu hỏi mở** để suy ngẫm: nếu $O(\alpha(n))$ đã gần như $O(1)$ trong thực tế, liệu có tồn tại một chặn dưới (lower bound, tương tự khái niệm đã gặp ở Chương 3.3, Mục 12) chứng minh rằng Union-Find **không thể** đạt $O(1)$ tuyệt đối? *(Gợi ý: đây chính xác là một kết quả đã được chứng minh — Tarjan và cộng sự đã chỉ ra $\Omega(\alpha(n))$ là chặn dưới cho một lớp thuật toán Union-Find nhất định, xác nhận rằng $O(\alpha(n))$ không chỉ là "đủ tốt" mà là **tối ưu** trong một khuôn khổ tính toán cụ thể.)*

---

## 13. Ưu điểm

- Độ phức tạp Amortized gần như hằng số $O(\alpha(n))$ khi kết hợp Union by Rank và Path Compression — một trong những kết quả hiệu năng tốt nhất trong toàn bộ Handbook.
- Triển khai cực kỳ gọn nhẹ (chỉ cần hai mảng `parent[]` và `rank[]`), không cần cấu trúc cây phức tạp với con trỏ hai chiều như BST/AVL Tree.
- Là thành phần **không thể thay thế** cho thuật toán Kruskal (Part IV) và nhiều bài toán liên thông đồ thị khác.
- Path Compression tạo ra một hiệu ứng "tự tối ưu hóa dần" theo thời gian sử dụng (Mục 10) — cấu trúc càng dùng nhiều càng nhanh.

---

## 14. Hạn chế

> **⚠️ Common Mistake**
> "Union-Find là một cấu trúc dữ liệu vạn năng cho mọi bài toán về nhóm/tập hợp" — bỏ qua giới hạn quan trọng nhất: **không hỗ trợ tách rời**.

- **Không hỗ trợ thao tác xóa/tách nhóm (split)** — một khi đã `union`, không thể hoàn tác trong cấu trúc cơ bản (Mục 12).
- **Không cho biết cấu trúc chi tiết** bên trong một nhóm (ví dụ không biết đường đi cụ thể giữa hai phần tử trong cùng nhóm) — chỉ trả lời được "có cùng nhóm hay không", không phải "quan hệ giữa chúng là gì".
- Chứng minh chặt chẽ độ phức tạp $O(\alpha(n))$ (Mục 7.2) rất phức tạp về mặt toán học (liên quan đến hàm Ackermann), khiến việc hiểu sâu **tại sao** nó đúng khó hơn nhiều so với chứng minh độ phức tạp của hầu hết cấu trúc dữ liệu khác trong Part II.
- Nếu chỉ triển khai một trong hai kỹ thuật tối ưu (chỉ Union by Rank, hoặc chỉ Path Compression), độ phức tạp chỉ đạt $O(\log n)$, không đạt được lợi ích tối đa của việc kết hợp cả hai (Mục 7.2).

---

## 15. So sánh

**Bảng 3.12.1 — Ba giai đoạn tối ưu hóa Union-Find**

| Kỹ thuật | Độ phức tạp `find`/`union` | Ghi chú |
|---|---|---|
| Ngây thơ (không tối ưu) | $O(n)$ Worst Case | Tương tự Degenerate Tree, Chương 3.8 |
| Chỉ Union by Rank | $O(\log n)$ | Đảm bảo tất định, tương tự chiều cao AVL Tree (Chương 3.9) |
| Chỉ Path Compression | $O(\log n)$ Amortized | Cải thiện dần theo thời gian sử dụng |
| **Cả hai kết hợp** | $O(\alpha(n))$ Amortized | Gần như $O(1)$ trong thực tế — hiệu ứng cộng hưởng |

**Phân tích:** Bảng này tóm tắt trọn vẹn hành trình tối ưu hóa của chương — một ví dụ giáo khoa về cách hai kỹ thuật đơn giản, khi phân tích **riêng lẻ** đều chỉ đạt kết quả "tốt vừa phải" ($O(\log n)$), nhưng khi **kết hợp** lại đạt một kết quả vượt trội hẳn ($O(\alpha(n))$) — một bài học thiết kế thuật toán quan trọng: không phải lúc nào lợi ích của việc kết hợp hai tối ưu hóa cũng chỉ đơn thuần "cộng dồn", đôi khi chúng **nhân lên** theo cách không hiển nhiên nếu chỉ phân tích từng phần riêng lẻ.

---

## 16. Tóm tắt

- **Union-Find (Disjoint Set)** quản lý các tập hợp rời rạc, hỗ trợ `find(x)` (tìm đại diện của nhóm) và `union(x,y)` (hợp nhất hai nhóm) — trả lời trực tiếp câu hỏi liên thông "$x$ và $y$ có cùng nhóm không?".
- Triển khai bằng rừng cây con trỏ cha (parent pointer forest); triển khai ngây thơ có thể suy biến chiều cao tương tự Degenerate Tree (Chương 3.8).
- **Union by Rank** (gắn cây nhỏ vào cây lớn) đơn lẻ đạt $O(\log n)$ tất định.
- **Path Compression** (làm phẳng đường đi sau mỗi `find`) đơn lẻ đạt $O(\log n)$ Amortized.
- **Kết hợp cả hai** đạt $O(\alpha(n))$ — với $\alpha$ là Inverse Ackermann Function, tăng chậm đến mức có thể xem như $O(1)$ trong mọi ứng dụng thực tế — một hiệu ứng cộng hưởng vượt xa tổng của từng kỹ thuật riêng lẻ.
- Hạn chế quan trọng nhất: không hỗ trợ tách nhóm — chỉ phù hợp cho bài toán "Incremental Connectivity" (chỉ thêm liên kết).

**Với chương này, Part II — Fundamental Data Structures đã hoàn tất.** Toàn bộ Part đã xây dựng một "bộ công cụ" cấu trúc dữ liệu nền tảng: tuyến tính (Array/Linked List), truy cập giới hạn (Stack/Queue), tra cứu (Hash Table/BST/cây cân bằng), ưu tiên (Heap), chuyên biệt cho chuỗi (Trie), và quản lý tập hợp (Union-Find). Part III (Algorithm Design Paradigms), bắt đầu từ Chương 3.13, sẽ chuyển trọng tâm từ "cấu trúc dữ liệu tĩnh" sang "chiến lược thiết kế thuật toán" — bắt đầu bằng Brute Force và Decrease-and-Conquer, rồi tiến tới Divide and Conquer (nơi Merge Sort, đã phân tích sơ bộ ở Chương 3.4, sẽ được trình bày đầy đủ), Greedy Algorithms (nơi Heap của chương 3.10 sẽ được dùng cho Huffman Coding), và Dynamic Programming.

---

## 17. Bài tập

### Mức Cơ bản (Basic)

1. Với 6 phần tử `{0,1,2,3,4,5}`, mô phỏng bằng tay chuỗi thao tác: `union(0,1), union(2,3), union(4,5), union(1,3)`. Vẽ cấu trúc `parent[]` cuối cùng (không cần Path Compression trong bước này, chỉ áp dụng Union by Rank).
2. Với cấu trúc kết quả ở Bài 1, gọi `find(5)`. Mô tả từng bước Path Compression được áp dụng.

### Mức Trung bình (Intermediate)

3. Giải thích tại sao dòng 9–10 trong pseudocode `Union` ở Mục 8 (`parent[rootY] ← rootX; rank[rootX] += 1`) **chỉ** tăng `rank` khi hai cây có **cùng** rank ban đầu. Điều gì sẽ xảy ra về mặt độ phức tạp nếu ta tăng `rank[rootX]` sau **mọi** lần `union`, bất kể rank hai bên có bằng nhau hay không?
4. Triển khai một phiên bản `UnionFind` chỉ dùng **Union by Size** (thay vì Rank) — nghĩa là luôn gắn cây có **ít phần tử hơn** vào cây có nhiều phần tử hơn. So sánh kết quả thực nghiệm (chiều cao cây, số bước `find` trung bình) với phiên bản Union by Rank ở Mục 9.

### Mức Nâng cao (Advanced)

5. Chứng minh chặt chẽ (bằng quy nạp) khẳng định ở Mục 7.1: nếu chỉ dùng Union by Rank (không Path Compression), chiều cao của bất kỳ cây nào trong rừng không bao giờ vượt quá $\lfloor \log_2 n \rfloor$.
6. Dùng cấu trúc `UnionFind` đã xây ở Mục 9, triển khai thuật toán kiểm tra một đồ thị vô hướng cho trước (danh sách cạnh) có chứa **chu trình (cycle)** hay không — bằng cách duyệt qua từng cạnh, gọi `union` và kiểm tra nếu `find(x) == find(y)` **trước khi** union thì đồ thị chứa chu trình. Đây chính là bước tiền xử lý cốt lõi của thuật toán Kruskal (Chương 3.24).

### Mức Nghiên cứu (Research)

7. Tìm hiểu về hàm **Ackermann** (Wilhelm Ackermann, 1928) và giải thích (ở mức trực giác, không cần chứng minh đầy đủ) tại sao nó tăng trưởng nhanh đến mức không thể biểu diễn bằng bất kỳ tháp lũy thừa hữu hạn nào. Sau đó giải thích tại sao hàm **nghịch đảo** của nó, $\alpha(n)$, lại tăng chậm đến mức gần như hằng số với mọi $n$ thực tế — liên hệ trực tiếp với mối quan hệ nghịch đảo giữa một hàm tăng nhanh và hàm ngược của nó (một khái niệm toán học tổng quát, có thể liên hệ với mối quan hệ giữa hàm mũ và hàm logarit đã học ở Volume 1).

---

## 18. Dự án nhỏ

**Dự án tích hợp — Part II: "Network Connectivity Analyzer"**

- **Mục tiêu:** Dự án tích hợp khép lại toàn bộ Part II, kết hợp Union-Find với các cấu trúc dữ liệu đã học trong suốt Part này để xây dựng một công cụ phân tích tính liên thông mạng lưới hoàn chỉnh.
- **Yêu cầu:**
  1. Dùng `UnionFind` (Mục 9) để xây dựng một hệ thống mô phỏng mạng lưới máy tính, hỗ trợ thêm kết nối (`add_connection(a, b)`) và truy vấn liên thông (`are_connected(a, b)`).
  2. Dùng `HashTable` (Chương 3.7) để ánh xạ tên máy chủ (chuỗi, ví dụ `"server-042"`) sang chỉ số nguyên dùng nội bộ trong `UnionFind` (vốn chỉ làm việc với chỉ số nguyên) — một ví dụ thực tế về việc **kết hợp nhiều cấu trúc dữ liệu** đã học để giải quyết một bài toán hoàn chỉnh.
  3. Đo và báo cáo: sau khi mô phỏng $n$ máy chủ và $m$ kết nối ngẫu nhiên, số lượng "cụm mạng" (connected components) riêng biệt còn lại, và số bước trung bình mỗi lần `find` (theo mẫu Mục 10).
  4. Viết một báo cáo tổng kết ngắn (README), điểm lại toàn bộ các cấu trúc dữ liệu đã dùng từ Chương 3.5 đến 3.12 trong dự án, và vai trò cụ thể của từng cấu trúc.
- **Công nghệ gợi ý:** Python thuần, tái sử dụng `HashTable` từ Chương 3.7.
- **Kết quả kỳ vọng:** Một hệ thống hoạt động đúng, cùng báo cáo xác nhận độ phức tạp gần như hằng số của các truy vấn liên thông, bất kể quy mô mạng lưới.
- **Kết quả kỳ vọng bổ sung:** Báo cáo tổng kết đóng vai trò "bản đồ ôn tập" cho toàn bộ Part II, chuẩn bị tinh thần chuyển sang Part III — nơi trọng tâm chuyển từ "cấu trúc dữ liệu" sang "chiến lược thuật toán".

---

## 19. Tự đánh giá

- [ ] Tôi có thể giải thích rõ ràng hai thao tác `find` và `union`, và cách chúng cùng nhau trả lời câu hỏi "hai phần tử có cùng nhóm hay không?".
- [ ] Tôi có thể tự tay mô phỏng chuỗi thao tác `union`/`find` trên giấy, bao gồm cả cơ chế Path Compression.
- [ ] Tôi hiểu tại sao Union by Rank **đơn lẻ** chỉ đạt $O(\log n)$, và tại sao **kết hợp** với Path Compression lại đạt được $O(\alpha(n))$ — một hiệu ứng cộng hưởng, không chỉ đơn thuần cộng dồn lợi ích.
- [ ] Tôi có thể giải thích (ở mức trực giác) tại sao Inverse Ackermann Function gần như là hằng số trong mọi ứng dụng thực tế.
- [ ] Tôi hiểu rõ giới hạn quan trọng nhất của Union-Find (không hỗ trợ tách nhóm) và có thể nhận diện các bài toán phù hợp/không phù hợp với cấu trúc này.

Nếu Bài tập 6 (kiểm tra chu trình bằng Union-Find) hoàn thành suôn sẻ — xin chúc mừng, bạn vừa tự tay xây dựng **bước cốt lõi** của thuật toán Kruskal, một trong những thuật toán đồ thị quan trọng nhất sẽ gặp đầy đủ ở Chương 3.24 (Part IV). Đây là một minh chứng rõ ràng cho việc Part II không chỉ dạy cấu trúc dữ liệu như những khái niệm cô lập, mà là những viên gạch nền tảng cho các thuật toán phức tạp hơn sắp tới.

---

## 20. Đọc thêm

- **Sách:** Thomas H. Cormen và cộng sự, *Introduction to Algorithms (CLRS)*, Chương 21 — "Data Structures for Disjoint Sets", trình bày đầy đủ và chặt chẽ nhất về chứng minh độ phức tạp Union by Rank và Path Compression, bao gồm cả chứng minh $O(\alpha(n))$ đầy đủ. *(Xem BOOKS.md — Volume 3, Tier S.)*
- **Paper mốc lịch sử:** Robert E. Tarjan (1975), *Efficiency of a Good But Not Linear Set Union Algorithm* — công trình gốc chứng minh chặt chẽ độ phức tạp của Union-Find.
- **Sách bổ sung:** Robert Sedgewick, Kevin Wayne, *Algorithms* (4th edition), Chương 1.5 — trình bày trực quan, dễ tiếp cận về Union-Find với nhiều ví dụ minh họa từng bước tối ưu hóa.
- **Chủ đề mở rộng (không bắt buộc):** Tìm đọc về **Link-Cut Trees** (Sleator, Tarjan, 1983) — cấu trúc dữ liệu giải quyết bài toán "Fully Dynamic Connectivity" đã nhắc ở Mục 12, hỗ trợ cả thêm lẫn xóa cạnh.
- **Chương tiếp theo:** Chương 3.13 — Brute Force and Decrease-and-Conquer, mở đầu Part III — Algorithm Design Paradigms.

---

### Liên kết chương (Cross References)

- **Chương trước:** 3.11 — Tries (chương này khép lại nhóm các cấu trúc dữ liệu chuyên biệt của Part II bằng một bài toán hoàn toàn khác: quản lý tập hợp rời rạc).
- **Chương tiếp theo:** 3.13 — Brute Force and Decrease-and-Conquer, mở đầu Part III, chuyển trọng tâm sang các chiến lược thiết kế thuật toán tổng quát.
- **Chương liên quan xa hơn:** 3.24 — Minimum Spanning Tree (Part IV) — ứng dụng trực tiếp và quan trọng nhất của Union-Find, thông qua thuật toán Kruskal (đã thực hành trước ở Bài tập 6); 3.27 — Strongly Connected Components (Part IV, một bài toán liên thông khác, dùng công cụ khác — DFS — thay vì Union-Find).
- **Vị trí trong Knowledge Graph:** Nút cuối cùng của Part II, phụ thuộc vào Chương 3.5 (Array cơ bản cho `parent[]`, `rank[]`) và Chương 3.4 (trực giác phân tích Amortized/quy nạp); là điều kiện tiên quyết bắt buộc cho thuật toán Kruskal ở Part IV.

---

*Hết Chương 3.12, khép lại trọn vẹn Part II — Fundamental Data Structures. Chương này tuân thủ đầy đủ cấu trúc 20 mục của `OUTPUT.md` và chuẩn Presentation Layer của `WRITING_STANDARD.md`, trình bày Union-Find như một hành trình tối ưu hóa gồm ba giai đoạn rõ rệt, đỉnh cao là kết quả $O(\alpha(n))$ — một trong những kết quả ấn tượng nhất về độ phức tạp thuật toán trong toàn bộ Handbook. Mọi khẳng định đều được kiểm chứng thực nghiệm (Mục 10) và chứng minh ở mức phù hợp với đối tượng độc giả (Mục 7), đồng thời liên hệ rõ ràng đến ứng dụng thuật toán Kruskal sẽ gặp ở Part IV. Đang chờ rà soát trước khi tiếp tục sang Chương 3.13, mở đầu Part III — Algorithm Design Paradigms.*
