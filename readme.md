# ELIXIR REPORT

## 1. Basic Type
### 1.1. Pattern Matching
Dấu ‘=’ trong Elixir là một match operator khác với assign operator trong Python.
```python
iex> x = 1 
1
iex> 1 = x
1
iex> 2 = x
** (MatchError) no match of right hand side value: 1
```
Pin operator ‘^’: Giá trị của biến không thể gán với một giá trị khác ngoài giá trị hiện tại.
```
iex> x = 1 
1 
iex> ^x = 2 
** (MatchError) no match of right hand side value: 2
```
Bỏ qua giá trị với _ (Underscore)
```
iex> [1, _, _] = [1, 2, 3]
[1, 2, 3]
```
### 1.2. Dữ liệu bất biến (Immuatable data)

-	Trong Elixir tất cả các cấu trúc dữ liệu là không thể thay đổi, không còn cách nào khác ngoài việc tạo một cấu trúc dữ liệu mới từ cấu trúc dữ liệu có sẵn.
```
iex> list1 = [ 3, 2, 1 ]
[3, 2, 1]
iex> list2 = [ 4 | list1 ]
[4, 3, 2, 1]
```
### 1.3. Kiểu dữ liệu
#### Giống Python: 
Integers, Float, Regular Expressions, Boolean.
#### Khác Python: 
- Atom: Một biến không thay đổi với tên là giá trị của chúng.
```
iex> :foo
:foo
```
- String: String trong Elixir đơn giản là một chuỗi các byte. 
```
iex> string = <<104,101,108,108,111>>
"hello"
```
- Char Lists: String của Elixir được bọc trong dấu ngoặc kép, trong khi char lists (chuỗi kí tự) được bọc trong dấu ngoặc đơn.
```
iex> char_list = 'hello'
'hello'

iex> [hd|tl] = char_list
'hello'

iex> {hd, tl}
{104, 'ello'}
```

### 1.4.	Collection Type
-	List: (danh sách) là một tập hợp các giá trị, có thể bao gồm nhiều kiểu dữ liệu; có thể bao gồm giá trị lặp:
```
iex> [ 1, 2, 3 ] ++ [ 4, 5, 6 ] # concatenation
[1, 2, 3, 4, 5, 6]
iex> [1, 2, 3, 4] -- [2, 4] # difference
[1, 3]
```
-	Tuples: cũng tương tự như list nhưng được lưu trữ một cách liên tục trên bộ nhớ.
```
{ 1, 2 } { :ok, 42, "next" } { :error, :enoent }
```
-	Keyword List: Là kiểu dictionary, là một tuple đặc biệt, với phần tử đầu là một atom.
```
iex> [foo: "bar", hello: "world"] [foo: "bar", hello: "world"]
```
-	Map: cho phép dùng bất kỳ kiểu dữ liệu nào cho khoá và các khoá không theo thứ tự sắp xếp.
```
iex> map = %{:foo => "bar", "hello" => :world} %{:foo => "bar", "hello" => :world}
```
- Cập nhật một map
```
iex> map = %{foo: "bar", hello: "world"}
%{foo: "bar", hello: "world"}

iex> %{map | foo: "baz"}
%{foo: "baz", hello: "world"}
```
-	Binaries: Cấu trúc binary được thể hiện bởi << và >>
```
iex> bin = << 1, 2 >> 
<<1, 2>>
iex> byte_size bin
2
```

### 1.5.	Anonymous Functions:
-	Python:
```
add = lambda x, y -> x + y
print add(2, 3) # Output: 5
```
-	Elixir:
```
iex> sum = fn (a, b) -> a + b end
#Function<12.17052888 in :erl_eval.expr/5>
iex> sum.(1, 2)
3
```

## 2. Module và Function
### 2.1.	Module
-	Dùng để gom nhóm, tổ chức các hàm vào một namespace.
```
defmodule Example do
  def greeting(name) do
    "Hello #{name}."
  end
end
```

```
iex> Example.greeting "Sean"
"Hello Sean."
```
### 2.2.	Struct
-	Struct là một tập các khoá và các giá trị mặc định.
```
defmodule Example.User do 
	defstruct name: "Sean", roles: [] 
end
```
-	Tạo một struct
```
iex> %Example.User{} 
#Example.User<name: "Sean", roles: [], ...> 
iex> %Example.User{name: "Steve"} 
#Example.User<name: "Steve", roles: [], ...>
```
-	Cập nhật một struct, tương tự như cập nhật một map
```
iex> steve = %Example.User{name: "Steve"}
#Example.User<name: "Steve", roles: [...], ...>
iex> sean = %{steve | name: "Sean"}
#Example.User<name: "Sean", roles: [...], ...>
```
### 2.3.	Function
-	Hàm được đặt tên
```
defmodule Greeter do 
  def hello(name) do "Chào " <> name 
  end 
end 
```
```
iex> Greeter.hello("Minh") "Chào Minh"
```
-	Hàm private

 Hàm private chỉ được gọi trong nội bộ của module đó
```
defmodule Greeter do 
	def hello(name), do: phrase <> name 
	defp phrase, do: "Chào " end
```

```
iex> Greeter.hello("Minh") "Chào Minh"
```
### 2.4. Pipe operator
Toán tử pipe |> truyền kết quả của một biểu thức như là tham số đầu tiên của một biểu thức khác.

Cách lồng ghép hàm thông thường:
```
foo(bar(baz(new_function(other_function()))))
```
Đoạn code tương tự được viết lại bằng pipe operator
```
other_function() |> new_function() |> baz() |> bar() |> foo()
```

## 3. Enum
Enum được sử dụng nhiều nhất trong tất cả các thư viện Elixir. Sử dụng nó để lặp lại, lọc, kết hợp, phân tách và thao tác các collection. Dưới đây là một số tác vụ phổ biến:

- Chuyển đổi một collection thành list
```
iex> list = Enum.to_list 1..5 
[1, 2, 3, 4, 5]
```

- Nối collection
```
iex> Enum.concat([1,2,3], [4,5,6])
[1, 2, 3, 4, 5, 6]
iex> Enum.concat [1,2,3], 'abc' 
[1, 2, 3, 97, 98, 99]
```
- Tạo collection mới bằng cách gọi một hàm trên mỗi phần tử của collection đang có:
```

iex> Enum.map(list, &(&1 * 10))
[10, 20, 30, 40, 50]
iex> Enum.map(list, &String.duplicate("*", &1)) 
["*", "**", "***", "****", "*****"]
```
- Lấy phần tử từ một vị trí
```
iex> Enum.at(10..20, 3) 13
iex> Enum.at(10..20, 20) nil
iex> Enum.at(10..20, 20, :no_one_here) 
:no_one_here
iex> Enum.filter(list, &(&1 > 2))
[3, 4, 5]
iex> Enum.filter(list, &Integer.is_even/1) 
[2, 4]
iex> Enum.reject(list, &Integer.is_even/1) 
[1, 3, 5]
```
- Sắp xếp và so sánh các phần tử
```
iex> Enum.sort ["there", "was", "a", "crooked", "man"] 
["a", "crooked", "man", "there", "was"]
iex> Enum.sort ["there", "was", "a", "crooked", "man"],
... &(String.length(&1) <= String.length(&2)) 
["a", "man", "was", "there", "crooked"]
```
```
iex(4)> Enum.max ["there", "was", "a", "crooked", "man"]
"was"
```
```
iex(5)> Enum.max_by ["there", "was", "a", "crooked", "man"], &String.length/1 
"crooked"
```
- Tách collection
```
iex> Enum.take(list, 3)
[1, 2, 3]

iex> Enum.take_every list, 2
[1, 3, 5]

iex> Enum.take_while(list, &(&1 < 4))
[1, 2, 3]

iex> Enum.split(list, 3)
{[1, 2, 3], [4, 5]}

iex> Enum.split_while(list, &(&1 < 4)) 
{[1, 2, 3], [4, 5]}
```
- Nối collection
```
iex> Enum.join(list) 
"12345"
iex> Enum.join(list, ", ") 
"1, 2, 3, 4, 5"
```
- Dự đoán collection
```
iex> Enum.all?(list, &(&1 < 4)) 
false
iex> Enum.any?(list, &(&1 < 4)) 
true
iex> Enum.member?(list, 4) 
true
iex> Enum.empty?(list) 
false
```
- Kết hợp collection
```
iex> Enum.zip(list, [:a, :b, :c])
[{1, :a}, {2, :b}, {3, :c}]
iex> Enum.with_index(["once", "upon", "a", "time"]) 
[{"once", 0}, {"upon", 1}, {"a", 2}, {"time", 3}]
```
-  Thu gọn collection về một giá trị. Ta cần cung cấp một accumulator (giá trị tích luỹ) không bắt buộc (trong trường hợp này là 10) và một hàm để tính dựa trên accumulator này và các phần tử của collection; nếu không có accumulator, phần tử đầu tiên sẽ được sử dụng thay thế:
```
iex> Enum.reduce([1, 2, 3], 10, fn(x, acc) -> x + acc end)
16
iex> Enum.reduce([1, 2, 3], fn(x, acc) -> x + acc end)
6
iex> Enum.reduce(["a","b","c"], "1", fn(x,acc)-> x <> acc end)
"cba1"
```
## 4. Protocols
Protocol là cơ chế trong việc đạt được tính đa hình trong Elixir
```
defprotocol Blank do
  @doc "Returns true if data is considered blank/empty"
  def blank?(data)
end
defimpl Blank, for: List do
  def blank?([]), do: true
  def blank?(_), do: false
end
```
```
Blank.blank?([])  # → true
```
