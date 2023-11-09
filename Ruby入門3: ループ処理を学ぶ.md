****01:0から9までを表示してみよう - for in 受講済みにする****

```jsx
for カウンタ変数 in 繰り返す範囲 do # doは省略可能
繰り返し処理
end

for i in 0..9
    puts "hello"
end
```

***02:条件によるくり返し処理1 - while***

```jsx
for カウンタ変数 in 繰り返す範囲 do # doは省略可能
繰り返し処理
end

for i in 0..9
    puts "hello"
end
```

```jsx
# whileによるループ処理

HP = 30	# カウンタ変数を初期化
while HP >= 0
    hit = rand(1..10)
    puts "スライムに#{hit}のダメージを与えた"	# 繰り返し処理
    HP -= hit	# カウンタ変数を更新
end
```

```jsx
範囲演算子
i = 20
while 10 <= i && i <= 20
  puts i
  i -= 1
end
```

# ****05:繰り返しでHTMLを作成する****

```jsx
puts "<select name='age'>"
for age in 1..100
    puts "<option>#{age}歳</option>"
end

puts "</select>"
```

# ****03:条件によるくり返し処理2 - while 受講済みにする****

```jsx
# whileによるループ処理

i = 5	# カウンタ変数を初期化
while i <= 15
    puts i		# 繰り返し処理
    i = i + 2	# カウンタ変数を更新
end
puts "last #{i}"
```
