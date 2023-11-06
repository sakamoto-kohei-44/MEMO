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
