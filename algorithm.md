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
