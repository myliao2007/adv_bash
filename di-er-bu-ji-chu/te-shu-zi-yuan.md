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

註解也可以放在空白後面

```bash
     # A tab precedes this comment.
```

註解甚至可以嵌入在管線（pipe）裡面

```bash
initial=( `cat "$startfile" | sed -e '/#/d' | tr -d '\n' |\
# Delete lines containing '#' comment character.
           sed -e 's/\./\. /g' -e 's/_/_ /g'` )
# Excerpted from life.sh script
```

註解可能不能放在同一行的註解後面，註解沒有結束的方式，所以為了「確定程式碼可以執行」，還是建議把註解放在一行的開頭，然後把指令放到在下一行。

（TBC)



; 指令分隔符號（分號），允許將兩個或多個指令放在同一行上。

```bash
echo hello; echo there


if [ -x "$filename" ]; then    #  Note the space after the semicolon.
#+                   ^^
  echo "File $filename exists."; cp $filename $filename.bak
else   #                       ^^
  echo "File $filename not found."; touch $filename
fi; echo "File test complete."
```

(TBC)

