# Collection framework trong java? Sinh ra để làm gì? Có những collection nào?
## 1. Collections framework là gì?
Là tập hợp các **interface và class** có sẵn trong Java để lưu trữ và thao tác với các nhóm objects khác nhau.
Package: *java.util*
**Mục đích**
- Lưu trữ nhiều objects
- Thao tác dễ dàng (search, remove, add, sort)
- Cung cấp các cấu trúc dữ liệu chuẩn
## 2. Tại sao cần collections framework
- Trước khi có *Collections* (dùng Array):
	- Fixed size
	- Không có methods tiện lợi
	- Khó thao tác
- Sau khi Collections
	- Dynamic size
	- NHiều methods (add, remove, sort)
	- Đa dạng cấu trúc (List, Map, Set, Queue)
## 3. Collection Hierachy
```text
Collection (Interface)
├── List (Interface) - Có thứ tự, cho phép duplicate
│   ├── ArrayList
│   ├── LinkedList
│   └── Vector
│
├── Set (Interface) - Không thứ tự, không duplicate
│   ├── HashSet
│   ├── LinkedHashSet
│   └── TreeSet (SortedSet)
│
└── Queue (Interface) - FIFO
    ├── PriorityQueue
    └── ArrayDeque

Map (Interface) - Key-Value pairs
├── HashMap
├── LinkedHashMap
├── TreeMap (SortedMap)
└── Hashtable

```
## 4. Core interfaces

| Interface | Đặc điểm                      | Implementation                  |
| --------- | ----------------------------- | ------------------------------- |
| List      | Có thứ tự, có duplicate       | ArrayList, LinkedList, Vector   |
| Set       | Không thứ tự, không duplicate | HashSet, LinkedHashSet, TreeSet |
| Map       | Key-Value, unique key         | HashMap, LinkedHashMap, TreeMap |
| Queue     | FIFO                          | PriorityQueue, ArrayQueue       |
## 5. List interface
Implementation:
- LinkedList: doubly linked list, nhanh khi add/remove
- ArrayList: Dynamic size, nhanh khi get
- Vector: thread-safe array
## 6. Set interface
Implementations:
- HashSet: nhanh nhất, không thứ tự
- LinkedHashSet: giữ thứ tự insert
- TreeSet: tự động sort
## 7. Map inteface
Implementations:
- HashMap: nhanh nhất, không thứ tự
- LinkedHashMap: giữ thứ tự insert
- TreeMap: tự động sort theo key
- HashTable: thread-safe HashMap
## 8. Queue Interface
Implementations:
- PriorityQueue: priority-based (theo thứ tự ưu tiên)
- ArrayDeque: double-ended queue (2 đầu ra)
## 9. Khi nào dùng
**Dùng List khi**
- Cần thứ tự
- Cho phép duplicate
- Truy cập bằng index
**Dùng Set khi**
- Không quan tâm thứ tự
- Không được duplicate
- Check contains nhanh
**Dùng Map khi:**
- Cần key-value
- Lookup nhanh bằng key
- Cache, dictionary
**Dùng Queue khi:**
- Xử lý tuần tự (FIFO)
- Task queue, message queue
# Tóm tắt
**Collections Framework**: tập hợp các interface/class để lưu trữ và thao tác objects
**Core interfaces**: list, set, map (không extend Collection), queue
List: có thứ tự, có duplicate
Set: không thứ tự, unique
Map: Key - Value (HashMap, LinkedHashMap)
Queue: FIFO (PriorityQueue)
# ArrayList trong Java
ArrayList là gì? Cách hoạt động? Khi nào dùng ArrayList?
## 1. ArrayList là gì?
Là 1 implementation của List interface, sử dụng **dynamic array** để lưu trữ elements.
Đặc điểm:
- Dynamic size
- Có thứ tự
- Cho phép duplicate
- Cho phép null
- Not thread-safe
## 2. Performance

| Operation           | Time complexity | Lý do                    |
| ------------------- | --------------- | ------------------------ |
| get(index)          | O(1)            | lấy theo index           |
| add(element)        | O(1) amortized  | add cuối, đôi khi resize |
| add(index, element) | O(n)            | shift element            |
| remove(index)       | O(n)            | shift elements           |
| contains(element)   | O(n)            | duyệt mảng               |
## 3. Resize Mechanism
```java
// Initial capacity = 10
ArrayList<String> list = new ArrayList<>();

// Add 10 elements → OK
for (int i = 0; i < 10; i++) list.add("Item");

// Add thêm 1 → Resize
list.add("Item 11");  // Capacity = 10 * 1.5 = 15

```
**Resize cost**: O(n) vì phải đem n phần tử sang array mới

## 4. ưu/ Nhược điểm
**Ưu điểm**
- Random access nhanh
- Add cuối nhanh
- Memory efficient: chỉ lưu elements + empty slots
- Cache-friendly: elements liên tiếp trong memory
**Nhược điểm**
- Add/remove giữa chậm: do phải shift
- Tốn bộ nhớ cho empty slots
- **Resize overhead:** đôi khi copy toàn bộ
## 5. Khi nào nên dùng
NÊN dùng khi
- Truy cập index
- Add/remove cuối nhiều
- Ít add/remove giữa
- Hầu hết (default choice)
KHÔNG NÊN dùng khi
- Add/remove đầu nhiều: LinkedList
- Cần thread-safe: CopyOnWriteArrayList

# LinkedList
## 1. LinkedList là gì?
Là implementation của List và Deque sử dụng **doubly linked list** để lưu trữ elements.
Đặc điểm:
- Doubly linked list (có node prev và next)
- Có thứ tự (ordered)
- Cho duplicate
- Cho null
- Not thread-safe
- Implement cả List và Deque
## 2. Node structure
``` java
class Node<E> {
    E data;
    Node<E> prev;
    Node<E> next;
}
```
## 3. Performance

| Operation         | Time complexity | Lý do                       |
| ----------------- | --------------- | --------------------------- |
| get(index)        | O(n)            | phải duyệt node đầu -> cuối |
| add(element)      | O(1)            | Add cuối                    |
| addFirst(element) | O(1)            | Add đầu                     |
| addLast(element)  | O(1)            | Add cuối, link node         |
| removeFirst()     | O(1)            | Xóa theo index              |
| removeLast()      | O(1)            | Tìm theo index, unlink      |
| remove(index)     | O(n)            | tìm node trước              |
## 4. Ưu/ Nhược điểm
Ưu điểm
- Add/ remove đầu cuối nhanh:
- Ko resize
- Memory dynamic: allocate khi cần
- Implement Deque: dùng như Queue
Nhược điểm:
- Random access chậm: get(index) = O(n)
- Memory overhead: mỗi node tốn 2 references (prev, next)
- Cache-unfriendly: nodes rải rác trong memory
- Chậm hơn arrayList: hầu hết
## 5. Khi nào dùng?
- Add remove đầu cuối nhiều
- Implement Queue/ Dequeue
- Ko truy cập bằng index
KHÔNG NÊN khi:
- Truy cập index nhiều --> ArrayList
- Hầu hết --> ArrayList
## 6. LinkedList as Deque
```java
Deque<String> deque = new LinkedList<>();

// Add
deque.addFirst("A");   // O(1)
deque.addLast("B");    // O(1)

// Remove
deque.removeFirst();   // O(1)
deque.removeLast();    // O(1)

// Peek
deque.peekFirst();     // O(1)
deque.peekLast();      // O(1)

```
## 7. ArrayList vs LinkedList
### Bảng so sánh

| Tiêu chí            | ArrayList     | LinkedList         |
| ------------------- | ------------- | ------------------ |
| Internal Structure  | Dynamic array | Doubly Linked List |
| get(index)          | O(1) ⭐        | O(n)               |
| add(element)        | O(1) ⭐        | O(1) ⭐             |
| add(index, element) | O(n)          | O(n)               |
| addFirst(element)   | O(n)          | O(1)⭐              |
| addLast(element)    | O(1)⭐         | O(1) ⭐             |
| remove(index)       | O(n)          | O(n)               |
| removeFirst()       | O(n)          | O(1)⭐              |
| removeLast()        | O(n)          | O(1) ⭐             |
| Memory              | Ít hơn ⭐      | Node reference     |
| Cache               | friendly      | not friendly       |

# HashMap
## 1. **HashMap là gì? Cách hoạt động? Khi nào dùng HashMap?**
Là implementation của Map interface, sử dụng **hash table** để lưu trữ key-value pairs. Key là unique, không có thứ tự
## 2. Internal structure
- Array of buckets
- Mỗi buckets chứa linked list (tree nếu collision)
- Default capacity: 16
- Load factor: 0.75
## 3. Performance

| Operation        | Time Complexity |
| ---------------- | --------------- |
| put(key, value)  | O(1)            |
| get(key)         | O(1)            |
| remove(key)      | O(1)            |
| containsKey(key) | O(1)            |
| size()           | O(1)            |
Worse case: O(n) khi tất cả key đều hash vào 1 bucket
## 4. Ưu nhược điểm
**Ưu điểm**
- **Nhanh nhất**: O(1) với put, get, remove
- **Flexible**: key-value pairs
- **Null support**: 1 null key, nhiều null values
**Nhược điểm**
- Không thứ tự
- Not thread-safe
- Memory Overhead: buckets + load factor
## 5. Khi nào dùng HashMap
Nên dùng khi
- Cần key-value pair
- Lookup nhanh bằng key
- Unordered
- Cache, dict, counting
Không nên dùng khi
- cần thứ tự --> LinkedHashMap
- Cần sort --> TreeMap
- Cần thread-safe --> ConcurrentHashMap
## 6. Custom Objects as Key
Phải override hashCode() và equals()
```java
class User {
	private String id;
	
	@Override
	public int hashCode() {
		return Objects.hash(id);
	}
	
	@Override
	public boolean equals(Object obj){
		if(this == obj) return true;
		if (!(obj instanceof User)) return false; 
		return Objects.equals(id, ((User) obj).id);
	}
}
Map<User, String> map = new HashMap<>();
map.put(new User("1"), "John");
```
**Tóm tắt:**
- HashMap: Hash table, key-value, không thứ tự
- Performance: O(1) cho put, get, remove
- Key unique: hashCode() và equals()
- Ưu điểm: nhanh nhất, flexible
- Nhược điểm: không thứ tự, not thread-safe
- DÙng khi: lookup nhanh, cache, dictionary

# HashSet
## 1. **HashSet là gì? Cách hoạt động? Khi nào dùng HashSet?**
HashSet là implementation của Set inteface, sử dụng HashMap internally để lưu trữ elements. Các giá trị là unique, unordered
**Đặc điểm:**
- Unique
- Không thread-safe
- Unordered
- Nhanh nhất trong các loại Set
## 2. Internal structure
Sử dụng HashMap internally:
- Element = key trong hashMap
- Value = dummy object
- Unique vì HashMap key là unique
## 3. Performance

| Operation         | Time complexity |
| ----------------- | --------------- |
| add(element)      | O(1)            |
| remove(element)   | O(1)            |
| contains(element) | O(1)            |
| size()            | O(1)            |
| Iteration         | O(n)            |
## 4. How it works
Hash-based
```java
HashSet<String> set = new HashSet<>();
set.add("java");
// 1. tính hashCode() của "Java"
// 2. Tìm bucket trong hashMap
// 3. Check equals() nếu có collision
// 4. Add nếu chưa tồn tại
```
Uniqueless:
- Dùng **hashCode**() và **equals**() để check duplicate
- Override 2 method để check với custom object
## 5. Ưu nhược điểm
Ưu điểm:
- **Nhanh nhất**: O(1) cho add, remove, contains
- **Unique**: tự loại duplicate
- **Memory efficient**: không lưu duplicate
Nhược điểm:
- **Không thứ tự**
- **Không thread-safe**
- **Phụ thuộc hashCode()**: performance phụ thuộc hash function
## Tóm tắt
**HashSet:** dùng HashMap internally, unique, unorder
**Performance**: O(1) với add, remove, contains
**Không thứ tự**
**Ưu điểm**: nhanh, unique, memory efficient
**Nhược điểm**: phụ thuộc hash function, not thread-safe
Dùng khi: cần unique + performance cao