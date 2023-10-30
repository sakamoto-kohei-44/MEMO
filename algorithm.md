＊＊gets.split(' ') |半角スペースで区切る＊＊<br>
| N += 3286 | N に 3,286 を足した値を N へ代入する(N = N + 3286)と同じ意味 |
| A, B, C = gets.split.map(&:to_i)
N = 0
N += A
N *= B
N %= C
puts N | 変数 N を 0 で初期化する
N に A を足した値を N へ代入する
N に B をかけた値を N へ代入する
N を C で割ったあまりを N へ代入する
N を出力する |
| a, b, c = gets.split.map(&:to_i)
if a * b <= c
puts "YES"
else
puts "NO"
end | 整数 A, B, C が与えられます。式 A × B ≦ C が成立している場合はYESを、そうではない場合はNOを出力してください。 |
| n = gets.to_i
if n == 7
puts "Yes"
else
puts "No"
end | ある占いでは、箱の中に 1~9 までのいずれかの数字が書かれている玉を取り出し、その玉に書かれている数字から運勢を占います。玉に書かれている数字が 7 の時は大吉となります。占いで取り出した玉に書かれている数字が 1 つ与えられます。大吉かどうかを判定してください。 |
| N = gets.to_i
(1..N).each do |i|
puts i
end
解説
1..Nの範囲オブジェクトを生成しそれをeachメソッドでくりかす。
puts iで現在の整数iを改行付きで表示する。putsはデフォで改行区切りになる。printだと空白区切り | 正の整数 N が与えられます。
1 ~ N の整数を 1 から順に改行区切りで出力してください。 |
| (1..100). each do |i|
if i%15 == 0
puts "FizzBuzz"
elsif i%3 == 0
puts "Fizz"
elsif i%5 == 0
puts "Buzz"
else
puts i
end
end | 1 ~ 100 の整数に対して、3 と 5 の両方で割り切れるなら FizzBuzz を、 3 でのみ割り切れるなら Fizz 、5 でのみ割り切れるなら Buzz を改行区切りで出力してください。また、どちらでも割り切れない場合は、その数字を改行区切りで出力してください。 |
| puts "1 1” | 2 つの 1 を半角スペース区切りで出力してください。 |
| a, b = gets.split.map(&:to_i)
result = a * b
puts result | 2つの正の整数a, bが改行区切りで入力されるのでaとbを掛け算した数値を出力してください。 |
| a, b = gets.split.map(&:to_i)
puts a + b
メモ
改行区切りだと
a = gets.to_i
b = gets.to_i
と書くが半角スペース区切りだと
a, b = gets.split.map(&:to_i)
と書く。 | ２つの正の整数 a, b が半角スペース区切りで入力されるので a と b を足した数を出力してください。 |
