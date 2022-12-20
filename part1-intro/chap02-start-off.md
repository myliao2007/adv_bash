---
description: SHELL程式設計是一種20世紀50年代的自動點唱機。— 拉里·瓦爾
---

# 第二章、從 #! 開始

在最簡單的情況下，指令碼只不過是儲存在檔案中的一串系統指令清單，這至少可以節省一些時間，在每次呼叫該特定的一串指令時可以不用重新打字。

**Example 2-1. **_**cleanup**_**: 清理 /var/log 目錄中的 log 檔案**

```bash
# Cleanup
# Run as root, of course.

cd /var/log
cat /dev/null > messages
cat /dev/null > wtmp
echo "Log files cleaned up."
```

這裡沒有什麼特殊的地方，只有一組指令同樣可以輕易地從控制台或是終端機視窗上依序執行，而把這些指令放在一個 script 中的好處，不只是不用重複重新這些指令，這個 script 成了一個程式（一個工具），而且很容易針對特定的應用程式進行修改或客製化。

**Example 2-2. **_**cleanup**_**: An improved clean-up script**

```bash
#!/bin/bash
# Proper header for a Bash script.

# Cleanup, version 2

# Run as root, of course.
# Insert code here to print error message and exit if not root.

LOG_DIR=/var/log
# Variables are better than hard-coded values.
cd $LOG_DIR

cat /dev/null > messages
cat /dev/null > wtmp


echo "Logs cleaned up."

exit #  The right and proper method of "exiting" from a script.
     #  A bare "exit" (no parameter) returns the exit status
     #+ of the preceding command. 

```

現在這個開始看起來像個真正的 script 了，只是我們可以再更進一步 ...

**Example 2-3. **_**cleanup**_**: 上述 script 的加強版。**

```bash
#!/bin/bash
# Cleanup, version 3

#  Warning:
#  -------
#  This script uses quite a number of features that will be explained
#+ later on.
#  By the time you've finished the first half of the book,
#+ there should be nothing mysterious about it.



LOG_DIR=/var/log
ROOT_UID=0     # Only users with $UID 0 have root privileges.
LINES=50       # Default number of lines saved.
E_XCD=86       # Can't change directory?
E_NOTROOT=87   # Non-root exit error.


# Run as root, of course.
if [ "$UID" -ne "$ROOT_UID" ]
then
  echo "Must be root to run this script."
  exit $E_NOTROOT
fi  

if [ -n "$1" ]
# Test whether command-line argument is present (non-empty).
then
  lines=$1
else  
  lines=$LINES # Default, if not specified on command-line.
fi  


#  Stephane Chazelas suggests the following,
#+ as a better way of checking command-line arguments,
#+ but this is still a bit advanced for this stage of the tutorial.
#
#    E_WRONGARGS=85  # Non-numerical argument (bad argument format).
#
#    case "$1" in
#    ""      ) lines=50;;
#    *[!0-9]*) echo "Usage: `basename $0` lines-to-cleanup";
#     exit $E_WRONGARGS;;
#    *       ) lines=$1;;
#    esac
#
#* Skip ahead to "Loops" chapter to decipher all this.


cd $LOG_DIR

if [ `pwd` != "$LOG_DIR" ]  # or   if [ "$PWD" != "$LOG_DIR" ]
                            # Not in /var/log?
then
  echo "Can't change to $LOG_DIR."
  exit $E_XCD
fi  # Doublecheck if in right directory before messing with log file.

# Far more efficient is:
#
# cd /var/log || {
#   echo "Cannot change to necessary directory." >&2
#   exit $E_XCD;
# }




tail -n $lines messages > mesg.temp # Save last section of message log file.
mv mesg.temp messages               # Rename it as system log file.


#  cat /dev/null > messages
#* No longer needed, as the above method is safer.

cat /dev/null > wtmp  #  ': > wtmp' and '> wtmp'  have the same effect.
echo "Log files cleaned up."
#  Note that there are other log files in /var/log not affected
#+ by this script.

exit 0
#  A zero return value from the script upon exit indicates success
#+ to the shell.
```

由於您可能不想清除整個系統日誌，因此這個版本的 **script** 將保留訊息紀錄的最後一個部分。您必須不斷探索並微調先前撰寫的 script，以提高效率。

\* \* \*

Script 開頭的 #! \[1] 是要告訴你的系統，說明這個檔案是一組指令，可以提供給指令直譯器執行。而 #! 實際上是兩個位元組（two bytes）的 \[2] 魔術數字，是一個指定檔案型別的特殊標籤，在本例中是一個可執行的shell script（請輸入 man magic 查詢關於這個有趣主題的詳細資訊）。緊跟著 #! 之後的是直譯器的路徑，此路徑用來解譯 script 中的程式，包含 shell, 程式語言或是工具程式。此指令直譯器接著會從頭開始執行 script 中的指令（在 #! 這行之後），而註解會被忽略 \[3]。

程式檔開頭的sha-bang(#!)\[1]會告訴您的系統，這個檔案是 script 的直譯器及其路徑。「#！」實際上是一個兩位元組的\[2]魔術數字，是一個指定檔案型別的特殊標籤，在本例中是一個可執行的 shell 指令碼（請輸入 man magic 瞭解有關此有趣主題的詳細資訊）。緊接在 #! 之後的是直譯器的路徑。無論是shell、程式設計語言或公用程式。指令直譯器接著就會執行 script 中的指令，從頭開始（ 從 #! 這行下面開始），並會跳過註解的部分。\[3]

```bash
#!/bin/sh
#!/bin/bash
#!/usr/bin/perl
#!/usr/bin/tcl
#!/bin/sed -f
#!/bin/awk -f
```

上列的每一行 script 表頭都是呼叫不同的指令直譯器，無論是預設的shell（在Linux系統中是bash）還是其它的。\[4] 使用 #!/bin/sh（大多數商業的 UNIX 變體中預設的 Bourne shell），使 script 可移植到非 Linux 機器上，儘管您犧牲了 Bash 特定的功能。但是，script 將符合POSIX \[5]sh 標準。

請注意，在「sha-bang」處指定的路徑必須正確，否則錯誤訊息（通常是「找不到指令」）將是執行script 的唯一結果。\[6]

如果script僅由一組通用系統指令組成，並且不使用內部shell指令，則可以省略 #!。上面的第二個範例需要初始化 #!，因為變數賦值行 lines=50 使用 shell 特定的構造。\[7]請再次注意，#!/bin/sh會呼叫預設的shell  解譯器，在 Linux 機器上預設為/bin/bash。

{% hint style="info" %}
本文件鼓勵使用模組化的方式來建立script，記錄並收集可能在未來script中有用的「樣板」程式碼片段。最終，你會建立相當龐大的日常用品庫。作為範例，以下script prolog 將測試是否已使用正確的引數數呼叫了script。
{% endhint %}

```bash
E_WRONG_ARGS=85
script_parameters="-a -h -m -z"
#                  -a = all, -h = help, etc.

if [ $# -ne $Number_of_expected_args ]
then
  echo "Usage: `basename $0` $script_parameters"
  # `basename $0` is the script's filename.
  exit $E_WRONG_ARGS
fi
```

很多時候，您會撰寫執行一個特定任務的script。本章的第一個script是範例。稍後，您可能會想到，將執行其他類似任務的 script 樣板化。用變數取代文字（「硬連線」）常數是朝這個方向邁出的一步，用函式取代重複的程式碼區塊亦是如此。

附註&#x20;

\[1] 在文學作品中通常被看成是母語。這是由語彙基元sharp(#)和bang(!)的串連所衍生的。

\[2] 一些UNIX型別（基於4.2 BSD的）據稱使用四位元組的魔術數字，要求在 ! -- 後面要有空格（#! /bin/sh）。根據斯文·馬斯克的說法，這可能是個神話。

\[3] shell script中的 #！這行將是指令直譯器(sh或bash)看到的第一件事。由於此行以#開頭，因此當指令直譯器最終執行script時，它將正確解釋為註釋。這條電話已經達到了目的（致電指令口譯員）。

如果script實際上包括了額外的 #！行，那麼bash會將其解釋為註解。

```bash
#!/bin/bash

echo "Part 1 of script."
a=1

#!/bin/bash
# This does *not* launch a new script.

echo "Part 2 of script."
echo $a  # Value of $a stays at 1.
```

這裡可以接受一些可愛的小把戲。

```bash
#!/bin/rm
# Self-deleting script.

# Nothing much seems to happen when you run this... except that the file disappears.

WHATEVER=85

echo "This line will never print (betcha!)."

exit $WHATEVER  # Doesn't matter. The script will not exit here.
                # Try an echo $? after script termination.
                # You'll get a 0, not a 85.


```

此外，請嘗試使用 #!/bin/more 啟動 README 檔案，使其成為可執行檔。執行結果是將檔案本身輸出。（在此，使用 cat 來檢視檔案可能是比較好的選擇，見範例19-3）。

\[5] 可攜式作業系統介面，嘗試標準化類 UNIX 作業系統。POSIX規範列在Open Group站點上。

\[6] 為了避免這種可能性，script 可以從 #!/bin/env bash sha-bang 這行開始。這在 bash 不位於 /bin 路徑的 UNIX 電腦可能很幫助。

\[7] 如果Bash是預設的shell，則 script 的開始不需要輸入#!。但是，如果從不同的 shell（如tcsh）啟動script，則需要 #!。
