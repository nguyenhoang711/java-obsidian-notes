# 1. JOIN
Pattern tìm kiếm khách hàng chưa mua gì
``` sql
SELECT k.full_name, k.email
FROM customers k
LEFT JOIN orders d ON k.id = d.customer_id
WHERE d.id IS NULL; 
```
-- pattern tìm "không có": LEFT JOIN ... WHERE bảng_phải.cột IS NULL

# 2. CASE WHEN
``` sql
CASE
    WHEN condition_1 THEN result_1
    WHEN condition_2 THEN result_2
    ELSE default_result
END
```
# 3. Thao tác chuỗi
UPPER(s)	Chữ hoa	UPPER('abc') → 'ABC'
LOWER(s)	Chữ thường	LOWER('ABC') → 'abc'
LENGTH(s)	Độ dài	LENGTH('Hello') → 5
SUBSTR(s, start, len)	Cắt chuỗi	SUBSTR('Hello', 1, 3) → 'Hel'
REPLACE(s, old, new)	Thay thế	REPLACE('a-b', '-', `_`) → a_b
CONCAT(a, b, ...)	Nối chuỗi — cách của MySQL / SQL Server, không dùng được trong sandbox SQLite	CONCAT('Hello', ' ', 'world') → 'Hello world'
# 4. EXISTS và IN ? Khi nào dùng

|           | EXISTS                        | IN                     |
| --------- | ----------------------------- | ---------------------- |
| Khi nào   | Subquery lớn                  | Subqueery nhỏ          |
| Hiệu suất | Nhanh hơn với bảng lớn        | OK với bảng nhỏ        |
| Đặc điểm  | Dừng date khi tìm thấy 1 dòng | Phải chạy hết subquery |
NOT EXISTS thay thế LEFT JOIN .... WHERE IS NULL
EXISTSL tham chiếu cột ngoài bảng  (correlated subquery)
# 5. Subquery + JOIN
```sql
SELECT p.product_name, sub.total_sold
FROM (
    SELECT product_id, SUM(quantity) AS total_sold
    FROM order_details
    GROUP BY product_id
    ORDER BY total_sold DESC
    LIMIT 3
) sub
INNER JOIN products p ON sub.product_id = p.id;
```
Subquery trong FROM -->? derived table
# 6. Pattern phân tích dữ liệu
Flow phân tích
- Nguồn dữ liệu (từ bảng nào)
- JOIN các bảng liên quan
- WHERE: lọc dữ liệu cần thiết
- GROUP BY: nhóm theo chiều phân tích
- HAVING: lọc theo nhóm (nếu cần)
- ORDER BY: sắp xếp kết quả
Lưu ý quan trọng:
- Derived table: phải có alias
- Query phức tạp: xây từng bước
# 7. Window Function
## 7.1 OVER & PARTITION BY
Tính toán trên tập hợp dùng liên quan đến các dòng hiện tại - nhưng không gộp các dòng lại
GROUP BY gộp 28 dòng thành 4 nhóm --> trả về 4 dòng. Window Functions giữ nguyên 28 dòng và thêm 1 cột kết quả
## 7.2 OVER() — Cửa sổ toàn bộ
SELECT full_name, salary,
       SUM(salary) OVER () AS tong_salary_cty
FROM employees
WHERE salary IS NOT NULL;

Mỗi dòng đều sẽ có 1 kết quả tổng hợp - cùng giá trị cho mọi dòng
## 7.3 PARTITION BY — Chia nhóm
SELECT full_name, department, salary,
       AVG(salary) OVER (PARTITION BY department) AS salary_tb_pb
FROM employees
WHERE salary IS NOT NULL;

PARTITION BY department: chia thành các "cửa sổ" theo phòng ban. Mỗi nhân viên lấy lương TB của phòng ban mình

## 7.4 So sánh với GROUP BY
				GROUP BY			Window Functions
Số dòng			Giảm (gộp)			Giữ nguyên
Cột chi tiết		Mất					Giữ
Use case		Báo cáo tổng		So sánh cá nhân vs nhóm

OVER(): cửa sổ toàn bộ bảng
OVER(PARTITION BY cột): cửa sổ theo nhóm
Window Functions không gộp dòng - là điểm khác biệt lớn nhất so với GROUP BY
## 7.5 ROW_NUMBER - đánh số thứ tự
```sql
SELECT full_name, department, salary,
       ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS stt
FROM employees
WHERE salary IS NOT NULL;
```
- Đánh số 1,2 ,3 cho mỗi phòng ban, lương giảm dần
## 7.6 RANK - xếp hạng có bỏ bậc
Nếu 2 người cùng hạng 2 sẽ xếp hạng người tiếp theo hạng 4 (bỏ hạng 3)
## 7.7  DENSE_RANK - Xếp hạng liên tục
Nếu 2 người cùng hạng 2 - người tiếp theo hạng 3 (không bỏ)

## Note
- ROW_NUBMER luôn cho số khác nhau (không trùng)
- RANK và DENSE_RANK: cho số trùng khi giá trị bằng nhau
- ORDER BY trong OVER() là bắt buộc với các hàm ranking
## 7.8 LAG & LEAD - So sánh dòng
- LAG(): lấy giá trị dòng trước
VD: 
```sql
SELECT strftime('%Y-%m', order_date) AS thang,
       SUM(total_amount) AS doanh_thu,
       LAG(SUM(total_amount)) OVER (ORDER BY strftime('%Y-%m', order_date)) AS dt_thang_truoc
FROM orders
WHERE status = 'Completed'
GROUP BY thang;
```

LAG(cột): trả về giá trị cột ở dòng trước đó. Dòng đầu tiên là NULL
- LEAD(): lấy giá trị dòng sau
LEAD(cột): giá trị dòng **tiếp theo**. Dòng cuối → NULL.
Ứng dụng: so sánh MoM
Tính % tăng trưởng so với tháng trước
```sql
SELECT thang, doanh_thu,
	ROUND((doanh-thu - dt_truoc) * 100 / dt_truoc, 1) AS tang_truong_pct
FROM
	(
		SELECT strftime('%Y-%m', order_date) AS thang,
		SUM(total_amount) AS doanh_thu,
		LAG(SUM(total_amount)) OVER (ORDER BY strftime('%Y-%m', order_date)) AS dt-truoc
		FROM orders
		WHERE status = 'Completed'
		GROUP BY thang
	);
	
```
## Note:
- LAG(column, n): lấy giá trị cách n dòng (n = 1)
- LAG(cột, 1, 0): tham số thứ 3 là giá trị mặc định thay NULL
- LAG, LEAD đều bắt buộc có ORDER BY trong OVER.
## 7.9 Running total - TỔng tích luỹ
```sql
SELECT
	order_date,
	total_amount,
	SUM(total_amount) OVER (ORDER BY order_date) AS luy_ke
FROM orders
WHERE status = 'Completed';
```
`SUM(order_amount) OVER (ORDER BY order_date)`tổng từ đầu dòng tới hiện tại

## 7.10 Frame clause
```sql
SELECT order_date, total_amount,
       AVG(total_amount) OVER (
           ORDER BY order_date
           ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
       ) AS ma_3
FROM orders
WHERE status = 'Completed';
```
Mặc định khi có ORDER BY: `ROWS BETWEEN UNBOUNDED PRECEDING AND CURENT ROW`
Tính TB 3 dòng gần nhất

## Note
- SUM OVER (ORDER BY ....): running total (mặc định)
- SUM OVER (PARTITION BY .... ORDER BY ...): running total trong mỗi nhóm
- Frame clause cho phép kiểm soát chính xác phạm vi tính toán
# 8. CTE & Set Operations
## 8.1 UNION - Gộp kết quả
Gộp kết quả bỏ trùng lặp
## 8.2 UNION ALL - Gộp không bỏ trùng lặp
Lưu ý với 2 cách này thì 2 lệnh SELECT sẽ phải cú cùng số lượng cột
## 8.3 INTERSECT - Giao nhau
Trả dữ liệu xuất hiện ở cả 2 query
## 8.4 EXCEPT - Loại trừ
Trả về dòng chỉ có ở query 1 mà không có ở query 2
## NOTE: kinh nghiệm chọn
- Hai nhánh không thể trùng nhau (gộp đơn hàng năm 2024 và 2025) --> dùng `UNION ALL`; bật khử lúc này là trả tiền cho việc không cần thiết
- Thật sự cần kết quả duy nhất --> `UNION` chấp nhận chi phí
- Tất cả lệnh đều cần trùng số cột
# 9. WITH RECURSIVE — CTE
## 9.1 khi nào cần đệ quy
CTE thường chỉ chạy 1 lần, nhưng sẽ có dữ liệu dạng phân cấp (Dạng cây) mà ko biết trước độ sâu:
- Cây quản lý
- Danh mục lồng nhau
- Chuỗi phụ thuộc: task A cần B, B cần C
Để duyệt hết mọi cấp, cần `CTE đệ quy (WITH RECURSIVE)` - một CTE tự tham chiếu chính nó cho đến khi không còn dòng mới
## 9.2 Cấu trúc WITH RECURSIVE
Một CTE đệ quy gồm 2 phần nối băgnf `UNION ALL`:
```sql
WITH RECURSIVE cte_name AS (
    -- 1) Anchor: the starting point, runs once
    SELECT ...

    UNION ALL

    -- 2) Recursive step: JOIN back to cte_name itself
    SELECT ...
    FROM some_table t
    JOIN cte_name r ON t.parent_col = r.child_col
)
SELECT * FROM cte_name;
```
- `Anchor` chạy trước, tạo tập kết quả ban đầu
- `Bước đệ quy`: lấy kết quả vòng trước (tham chiếu lại `cte_name`) để tìm dòng tiếp theo
- Engine lặp lại bước đệ quy đến khi `không sinh thêm dòng mới` -->  tự dừng
## 9.3 Cây quản lý nhân viên
Bảng employees có cột `manager_id` trỏ về `id` của người quản lý. Tìm toàn bộ cấp dưới của manager `id = 1`
```sql
WITH RECURSIVE subs AS (
	SELECT id, full_name, 1 AS level
	FROM employees
	WHERE manager_id = 1
	
	UNION ALL
	
	-- recursive step: subordinates of the people just found
	SELECT e.id, e.full_name, s.level + 1
	FROM employees e
	JOIN subs s ON e.manager_id = s.id
)
SELECT id, full_name, level
FROM subs
ORDER BY level, id;
```
	- Vòng 1 (anchor): lấy người có `manager_id = 1`--> gán `level = 1`
	- Vòng 2: lấy người báo cáo cho nhóm vừa tìm --> `level 2`
	- Lặp đến khi không còn cấp dưới nào
Cột level tăng dần qua mỗi vòng, cho biết độ sâu của nhân viên trong cây.
## 9.4 Tránh vòng lặp vô hạn
Có thể rơi vào vòng lặp vô hạn, cần có cách phòng tránh:
- Đảm bảo cây dữ liệu `không có vòng` - mõi node chỉ có 1 cha
- Thêm cột đếm độ sâu và điều kiện `WHERE level < N` để chặn cứng
## NOTE
`WITH RECURSIVE = anchor UNION ALL bước đệ quy (tự tham chiếu CTE)`
