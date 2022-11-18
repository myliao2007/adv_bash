---
description: SHELL程式設計是一種20世紀50年代的自動點唱機。— 拉里·瓦爾
---

# 從 #! 開始

在最簡單的情況下，指令碼只不過是儲存在檔案中的一串系統指令清單，這至少可以節省一些時間，在每次呼叫該特定的一串指令時可以不用重新打字。

**Example 2-1. **_**cleanup**_**: A script to clean up log files in /var/log**

```bash
# Cleanup
# Run as root, of course.

cd /var/log
cat /dev/null > messages
cat /dev/null > wtmp
echo "Log files cleaned up."
```

這裡沒有什麼特殊的地方，只有一組指令可以同樣輕易地從控制台或是終端機視窗上依序執行，將這些指令放在一個 script 中的好處不只是不用重複重新這些指令。這個 script 成了一個程式（一個工具），而且很容易針對特定的應用程式進行修改或客製化。

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

**Example 2-3. **_**cleanup**_**: An enhanced and generalized version of above scripts.**

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

由於您可能不想清除整個系統日誌，因此此版本的 **script** 將保留訊息紀錄的最後一個部分。您必須不斷探索並微調先前撰寫的 script，以提高效率。

\* \* \*

Script 開頭的 #! \[1] 是要告訴你的系統，說明這個檔案是一組指令，可以提供給指令直譯器執行。而 #! 實際上是兩個位元組（two bytes）的 \[2] 魔術數字，是一個指定檔案型別的特殊標籤，在本例中是一個可執行的shell script（請輸入 man magic 查詢關於這個有趣主題的詳細資訊）。緊跟著 #! 之後的是直譯器的路徑，此路徑用來解譯 script 中的程式，包含 shell, 程式語言或是工具程式。此指令直譯器接著會從頭開始執行 script 中的指令（在 #! 這行之後），而註解會被忽略 \[3]。

程式檔首部的sha-bang(#!)\[1]會告訴您的系統，這個檔案是一組要提供給所指示的命令解譯器的命令。「#！」實際上是一個兩位元組的\[2]魔術數字，是一個指定檔案型別的特殊標籤，在本例中是一個可執行的shell指令碼（請鍵入man magic瞭解有關此有趣主題的詳細資訊）。緊隨sha-bang之後的是路徑名稱。此路徑是解譯指令碼中指令的程式路徑，無論是shell、程式設計語言或公用程式。命令解譯器接著會執行指令碼中的命令，從頂端（sha-bang行之後的行）開始，並忽略註解。\[3]

```bash
#!/bin/sh
#!/bin/bash
#!/usr/bin/perl
#!/usr/bin/tcl
#!/bin/sed -f
#!/bin/awk -f
```

<mark style="color:red;">待續...</mark>
