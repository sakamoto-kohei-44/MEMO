# ****02:複数の条件を組み合わせてみよう****

```jsx
number = 3
if number == 1
    puts "好き"
elsif number == 2
    puts "どちらでもない"
else
    puts "嫌い"
end
```

```jsx
# if文による条件分岐
if 条件式1
   #条件式1が成立したときの処理
elsif 条件式2
   #条件式2が成立したときの処理
else
   #条件式が、どれも成立しなかったときの処理
end
```

# ****03:比較演算子で条件分岐してみよう****

```jsx
time = 14
if time < 12
    puts "午前中"
elsif time == 12
    puts "正午"
elsif time > 12
    puts "午後"
end
```

# ****04:おみくじを作ってみよう****

```jsx
omikuji = rand(1..10)
puts omikuji

if omikuji == 1
    puts "大吉"
elsif omikuji == 2
    puts "中吉"
elsif omikuji <= 4
    puts "小吉"
elsif omikuji <= 7
    puts "キョウ"
end
```

# ****05:RPGのクリティカルヒットを再現 受講済みにする****

```jsx
hit = rand(1..10)
puts hit

if hit < 6
    puts "スライムに#{hit}のダメージを与えた"
else
    puts "クリティカル！"
end
```

# ****06:西暦から平成何年を求めてみよう****

```jsx
**seireki = 2015
print "西暦#{seireki}年は、"

heisei = seireki - 1988
puts "平成#{heisei}年です"**
```

putsメソッド：自動的に改行

printメソッド：自動的に改行しません

# ****07:【補講】複数の条件を組み合わせてみよう - AND 受講済みにする****

```jsx
number1 = 1
number2 = 1
if number1 == 1 && number2 == 1
    puts "好き"
else
    puts "嫌い"
end

number3 = 4
if number3 >= 30 && number3 <= 60
    puts "あたり"
else
    puts "残念"
end
```

# ****08:【補講】複数の条件を組み合わせてみよう - OR 受講済みにする****

```jsx
number = rand(1..100)
puts number
if number <= 30 || number >= 60
    puts "あたり"
else
    puts "残念"
end
```

# ****09:【補講】条件式の評価結果を理解しよう****

論理演算

- 条件が成立したとき：真、true
- 条件が成立しなかったとき：偽、false

# ****10:【補講】データ型をさらに理解しよう****

データ型の種類

- 数値： 1, 2, 3
- 文字列： "hello","123"

数値データの種類

- 整数： 1, 2, 3, 10 Integer
- 実数： 3.3, 3.141592 Float
