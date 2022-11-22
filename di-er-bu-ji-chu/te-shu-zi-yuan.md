# 特殊字元

字元會有什麼特別的呢？如果字元超出本身詞彙上所具備的意義，那麼我們會把這樣的字元稱為特殊字元。除了指令與關鍵字之外，特殊字元也是Bash script 的組成之一。

在 script 與其他地方找到的特殊字元

\#

**註解 (comment).** 以 # 開頭那一行都是註解，所以不會執行這行的內容（#! 開頭的例外)

```bash
# This line is a comment.
```

註解也可以放在指令結尾

```bash
echo "A comment will follow." # Comment here.
#                            ^ Note whitespace before #
```



(TBC)
