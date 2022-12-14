# 第一章、shell 程式設計

沒有一種程式語言是可以萬用的，每個程式語言各有所長，工欲善其事，必先利其器。

\--Herbert Mayer



對於想要相當熟練地掌握系統管理的人，即使他們並不預期需要實際編寫 script，難免也是會需要操作 script。想想當Linux機器啟動時，它會執行 /etc/re.d 中的shell script來恢復系統配置和設定服務。詳細瞭解這些啟動 script 對於分析系統的行為，以及修改系統是很重要的。

Script 的技巧不難掌握，因為 script 可以按照小小部分構建，而且只有少數特定的 shell 操作元與選項會不好學。其語法很簡單（甚至很簡潔）類似在命令列中呼叫和連結公用程式，而且只有幾個「法則」在規範如何使用它們。大多數的簡短 script 可以一次寫好就順利執行，即使是較長的 script 也是很直覺的。

在個人電腦早期，BASIC語言使任何有相當熟練的電腦玩家都能在早期的微電腦上編寫程式。幾十年後，Bash script 語言使任何對Linux或UNIX有初步瞭解的人都能在現代機器上做同樣的事情。

我們現在有微型單板電腦，功能驚人，例如樹莓派。

Bash script 是探索這些引人入勝的裝置功能的方法。

Shell script 是一種快速而簡潔的方法，可用來製作複雜應用的原型。讓有限的功能子集在指令碼中工作，這通常是專案開發的第一階段。這樣，可以測試並修改應用程式的結構，並在繼續使用 C、C++、Java、Perl或Python的最終編碼之前發現主要缺陷。

Shell script 回到了傳統的UNIX哲學：將複雜的專案分解為更簡單的子作業，將元件和公用程式連結在一起。很多人認為，比起使用新一代功能強大的多合一語言(比如Perl)，這種語言更適合解決問題，或者說至少從美學角度來看更令人愉悅。這種語言試圖讓所有人擁有一切，但代價是不得不改變你的思維過程，以適應此工具。

根據Herbert Mayer的說法，「有用的語言需要陣列（array）、指標（pointer）和通用機制來構建資料結構。」如果根據這些標準，shell script 還算不上是「有用」的程式語言，或可能連程式語言都稱不上 ...

### 何時不要使用 shell script

* 資源密集型任務，特別是在執行速度很關鍵的情況(排序、雜湊、遞回……)
* 涉及繁重的數學運算的程式，特別是浮點運算、任意精度計算或複數(請改用C++或FORTRAN)
* 需要跨平台可攜性(請改用C或Java)
* 複雜的應用，其中需要結構化程式設計（變數的型別檢查、函式原型等）
* 關鍵任務型應用程式，您寄希望於公司的未來
* 安全性很重要，需要保證系統的完整性，防止侵入、破解和破壞的情況
* 專案包含具有互鎖相依性的子元件
* 需要大量的檔案操作(Bash僅限於序列檔案存取，且僅適用於
* 特別笨拙而沒有效率的風格，一行又一行的處理。
* 需要原生支援多維陣列
* 需要資料結構，例如鏈結串列（linked list）或樹狀結構
* 需要產生/處理圖形或圖形化使用者介面
* 需要直接存取系統硬體或外部周邊裝置
* 需要連線埠或 socket I/0
* 需要使用舊版程式碼的函式庫或介面
* 專有、封閉式原始碼的應用程式（Shell script 將原始碼完全公開，讓全世界都能看到）。

如果以上任何一種情況適用，請考慮使用功能更強大的指令碼語言 — 可能為Perl、Tel、Python、Ruby — 或者可能是編譯後的語言，例如C、C++或Java。即便如此，將應用程式的原型以 shell script 來寫，仍可能是一個有用的開發步驟。

我們將使用Bash，它是「Bourne-Again shell」的縮寫 \[3l] ，也是史蒂芬·伯恩（Stephen Bourne）如今經典的 Bourne shell 的雙關語。Bash 已經成為大多數 UNIX 的 shell sciprt 的實際標準。 本書所涵蓋的大部分原則同樣適用於其他 shell 撰寫的 script，如 Korn Shell，Bash的一些功能就是從這個 shell 中獲得的；ill 和 C Shell 及其變體。（請注意，由於Tom Christiansen在1993年10月的Usenet文章中指出，由於某些固有的問題，不建議使用 C Shell。 ）

接下來是關於 shell script 編寫的一個教學。它重度依賴範例來說明 shell 的各種功能。這些 script 範例是很管用的（已經盡量測試過了），其中一些甚至可以直接應用在現實生活。讀者可以從程式碼封存檔取得可以實際運作的原始碼來玩 (scriptname. sh 或 scriptname.bash)，\[我將它們的權限設定為可執行(chmod u+rx scriptname)，然後執行它們看看會怎樣。如果來源封存無法使用，則從 HTML 或 PDF 格式版本將程式碼剪下並貼上。請注意，此處介紹的某些 script 會有用到一些還未經解釋的功能，這可能會需要讀者暫時先不用理解，先跳過，先掌握精神。

除非另有說明，不然本文後續的 script 範例都是作者親自寫的。



_His countenance was bold and bashed not._

— 埃德蒙·斯賓塞（Edmund Spenser）
