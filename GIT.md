### GIT

#### GIT 底層

##### GIT 對象

在 GIT 內部大量使用SHA-1哈希算法，GIT 的核心部分是一個簡單的鍵值對數據庫，依據哈希算法的特性：相同輸入，哈希值也一定相同；不同輸入，哈希值必然不同。鍵是 SHA-1 算法輸出的哈希值，值為輸入 SHA-1 算法的內容。

`git hash-object` 命令為 GIT 的底層命令，它會將輸入的值經由 SHA-1 算法運算後，返回哈希值。

```
$ echo 'hello world' | git hash-object --stdin
3b18e512dba79e4c8300dd08aeb37f8e728b8dad
```

`--stdin`：從標準輸入讀入內容，不指定此選項，必須在命令尾部指定儲存文件路徑

`--w` ：寫入 GIT 資料庫

上面的命令會返回「hello world」的哈希值，加上`--w`選項可以將其存入資料庫。查看存入資料庫後`.git/object/`目錄下結構

剛創建的目錄結構：info 與 pack 都為空資料夾

<img src="C:\Users\happi\AppData\Roaming\Typora\typora-user-images\image-20210326095312353.png" alt="	" style="zoom:50%;" />

存入 hello world 字串後目錄結構

```
$ echo 'hello world' | git hash-object --stdin -w
3b18e512dba79e4c8300dd08aeb37f8e728b8dad
```

<img src="C:\Users\happi\AppData\Roaming\Typora\typora-user-images\image-20210326095612275.png" alt="image-20210326095612275" style="zoom:50%;" />

<img src="C:\Users\happi\AppData\Roaming\Typora\typora-user-images\image-20210326095707657.png" alt="image-20210326095707657" style="zoom:50%;" />

`objects`資料夾多出的3d資料夾，內部存放了一個看似以哈希值命名的檔案，仔細查看你會發現，資料夾名稱為 SHA-1 算法所輸出哈希值的前兩碼，文件名稱則是剩下的38碼。

`git cat-file <哈希值>` 命令查看該哈希值的內容

```
$ git cat-file -p 3b18e512dba79e4c8300dd08aeb37f8e728b8dad
hello world
$ git cat-file -t 3b18e512dba79e4c8300dd08aeb37f8e728b8dad
blob
```

 `-p`：友好顯示其值，不指定此項會顯示壓縮後的格式

`-t`：查看哈希值類型，有 blob 、tree、commit、tag 四種類型

通過`git cat-file`可以得知，存入到 GIT內部是 blob 類型的文件。**這些 blob 類型的文件，就是 GIT 對象。GIT 對象是存儲文件內容**

GIT 對象僅能代表某個文件當下的快照，顯然不能用來代表專案。專案的一個版本也不僅僅只修改一個文件。且記住所有文件每次快照對應的哈希值顯然是不現實的。樹對象就是解決方案。

##### 樹對象(tree object)

樹對象，它能解決文件名保存問題，也允許將多個文件組織到一起。GIT 以一種類似 UNIX 文件系統的方式儲存內容，其中樹對象代表目錄項，GIT 對象則對應文件內容。**一個樹對象包含一或多條紀錄(每條紀錄都含有一個指向 GIT 對象或者子樹對象的 SHA-1 指針，以及相應的模式、類型、文件名信息)。**

###### 構建樹對象

可以通過 `update-index`、`write-tree`、`read-tree`等命令來構建樹對象並塞至**暫存區**。

操作：

+ 利用 `update-index` 命令，將要暫存的文件加入至暫存區

  ```
  $ echo 'test v1' >> test.txt
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git hash-object -w ./test.txt
  warning: LF will be replaced by CRLF in ./test.txt.
  The file will have its original line endings in your working directory
  915c628f360b2d8c3edbe1ac65cf575b69029b61
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git update-index --add --cacheinfo 100644 915c628f360b2d8c3edbe1ac65cf575b69029b61 test.txt
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git ls-files -s
  100644 915c628f360b2d8c3edbe1ac65cf575b69029b61 0       test.txt
  
  ```

  建立 test.txt 文件，利用 `git hash-object` 命令寫入至數據庫中。透過 `git update-index` 命令，將數據庫的中 test.txt 文件快照加入至暫存區中。`git ls-files -s` 可以查看暫存區內容。

  `git update-index` 選項介紹：

  + `--add` 向暫存區添加內容，**如果文件名重複，會執行覆蓋操作**。

  + `-- cacheinfo` 要添加的 test.txt 已經被建立成 GIT 對象存放在數據庫中，該選項告訴 GIT 去數據庫找到該 GIT 對象。

  +  文件模式

    100644 表示這是一個普通文件、100755 表示可執行文件、120000表示一個符號連接。

  > `git update-index --add <文件名>` 可以幫你省略手動創建 GIT 對象並寫入數據庫的操作

+ 利用 `git write-tree` 命令，將暫存區中的所有內容，生成一個樹對象

  生成樹對象之前，先來看一下objects資料夾內容，目前只有剛建立的 GIT 對象

  ```
  $ find .git/objects/ -type f
  .git/objects/91/5c628f360b2d8c3edbe1ac65cf575b69029b61
  ```

  執行 `git write-tree`，並再次查看objects資料夾內容，生成的樹對象已經被放入資料夾中

  ```
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git write-tree
  72203871fa4668ad777833634034dcd3426879db
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ find .git/objects/ -type f
  .git/objects/72/203871fa4668ad777833634034dcd3426879db
  .git/objects/91/5c628f360b2d8c3edbe1ac65cf575b69029b61
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git cat-file -t 72203871fa4668ad777833634034dcd3426879db
  tree
  ```

> 一個樹對象代表項目的一個版本，一個 GIT 對象代表一個文件的一個版本

**項目的版本與版本必須要有所聯繫，才能達到版本控制。樹對象解決了文件訊息與 GIT 對象對應的問題，但它本身無法在描述更多訊息，這時就由「提交對象」來記錄這些訊息。**

##### 提交對象

提交對象用來描述樹對象，必然包含著這個版本項目的快照，也就是樹對象本身。除此之外，還有許多用來描述樹對象條目，共同組織成一個完整的版本訊息。GIT 對象、樹對象、提交對象組織成一個整體就如下圖所示。

+ parent：紀錄前一個提交對象的哈希值，由此依據可以前進後退版本。
+ tree：紀錄樹對象的哈希值，由此得知這個版本的項目內容快照。
+ author：記錄該版本的作者
+ committer：紀錄該版本提交者
+ data：紀錄該版本的提交日期

<img src="C:\Users\happi\AppData\Roaming\Typora\typora-user-images\image-20210326120122309.png" alt="image-20210326120122309" style="zoom:50%;" />

提交對象中的 parent 記錄該提交對象的父對象，如果版本的內容不源自提交，那麼 parent 為空，通常是第一個提交，該提交也被稱為「root commit」。

<img src="C:\Users\happi\AppData\Roaming\Typora\typora-user-images\image-20210326120427742.png" alt="image-20210326120427742" style="zoom:50%;" />

###### 創建提交對象

`git commit-tree -m <提交訊息> <樹對象哈希值>` 命令可以創建一個提交對象

```
happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
$ git commit-tree -m 'first commit' 72203871fa4668ad777833634034dcd3426879db
0053e8ad22ad20e8a12e9143d842cb27d8cfaf66

happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
$ git cat-file -p 0053e8ad22ad20e8a12e9143d842cb27d8cfaf66
tree 72203871fa4668ad777833634034dcd3426879db
author James <happily1994@gmail.com> 1616740309 +0800
committer James <happily1994@gmail.com> 1616740309 +0800

first commit
```

使用 `git cat-file -p` 查看該提交對象，可以看到類似格式的提交對象訊息。

`git commit-tree` 選項：

+ `-m`：輸入提交訊息
+ `-p`：指定父提交對象
+ `-F`：提交訊息由文件讀入

#### 創建版本庫

`git init` 命令會在當前資料夾創建本地版本庫，查看當前資料夾，會發現多一個`.git`隱藏資料夾，裡面記錄著 GIT 版本訊息。

#### 查看檔案狀態

檔案在 GIT 中被分為兩類，未追蹤 ( Untracked ) 和已追蹤 ( Tracked )。未追蹤的檔案表示尚未被納入 GIT 管理，反之則表示已被納入管理的檔案。已追蹤又分為3種狀態，已修改 ( Modified )、已暫存 ( Staged )、未修改 ( Unmodified )，已修改檔案表示工作目錄的檔案內容與版本庫不一致；已暫存表示修改的檔案已經被放至暫存區中、未修改表示工作目錄下的檔案與版本庫一致。

`git staged` 命令可以查看檔案的狀態，通過其他 GIT 命令，可以轉變檔案的狀態。如果剛創建本地庫，執行 `git status` 應該會看到以下訊息。

```
$ git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

這表示目前工作目錄與本地庫，也沒有新增的檔案。

當我們新增了一個檔案，`git status` 命令應該會看到以下訊息

```
happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
$ echo 'Hello World' >> test.txt

happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        test.txt

nothing added to commit but untracked files present (use "git add" to track)
```

GIT 會告訴我們有未追蹤文件 ( Untracking file ) test.txt，並以紅色字樣顯示。運行 `git add` 命令將其加至暫存區

```
happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
$ git add test.txt
warning: LF will be replaced by CRLF in test.txt.
The file will have its original line endings in your working directory

happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   test.txt
```

此時暫存區中有個新檔案 test.txt，並以綠色字樣顯示，現在檔案狀態為 ( Staged )。可以運行 `git commit ` 提交一個新版本至本地庫。

```
happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
$ git commit -m 'first commit'
[master (root-commit) fe18ab6] first commit
 1 file changed, 1 insertion(+)
 create mode 100644 test.txt

happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
$ git status
On branch master
nothing to commit, working tree clean
```

你會看到又回到一開始的訊息，目前 test.txt 檔案狀態為 ( Unmodified )。

##### 常用選項

下面列出 `git status` 常用的選項：

+ `git status -s` 或 `git status --short`

  列出簡潔的檔案狀態訊息。未追蹤的新檔案被標示為 ??、加入至暫存區的檔案被標示為 A、已修改檔案被標示為 M。標示分為兩個欄位，左邊欄位標示「暫存區」狀態；右邊欄位標示「工作目錄」狀態。所以在以下範例中，README 檔案室已修改但是尚未加入暫存區。Rakefile 檔案是已修改且加入暫存區，但是之後又做了修改，所以暫存區有預存了第一個修改，另一個沒有。

  ```
  $ git status -s
   M README
  MM Rakefile
  A  lib/git.rb
  M  lib/simplegit.rb
  ?? LICENSE.txt
  ```

#### 添加檔案至暫存區

`git add` 命令可以將**未追蹤的檔案**與**已修改的檔案**添加至暫存區。該命令接受「檔案」或「目錄」。如果是目錄，該命令會用遞歸方式加入那個目錄下的所有檔案。`git add` 是一個多重用途命令，它還能做一些其他的事，如「標記合併衝突檔案為已解決」。把它想成「把檔案內容加入下一次提交中」會比較容易理解。

```
$ git add README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
```

如果你對已暫存的檔案又做了一次修改，你可能會看到以下畫面，檔案同時處於已暫存和為暫存狀態。這是因為GIT 暫存的是你第一次對 CONTRIBUTING.md 運行 `git add` 時的修改，而你對 CONTRIBUTING.md 第二次修改並未暫存。如果你想將第二次修改加入下次提交中，那麼再次運行 `git add` 命令暫存第二次修改即可。

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

#### 忽略不需要的檔案

通常你會有一些不想讓 GIT 自動加入，也希望它們顯示為未追蹤，這些通常是自動產生的檔案，例如；日誌檔案或是編譯後所產生的檔案。這種情況下，你可以建立一個`.gitignore`的檔案，在該檔案中列舉符合這些檔名的 ( pattern )。以下是一個 `.gitignore` 範例檔內容：

```
# 不要追蹤檔名為 .a 結尾的檔案
*.a

# 但是要追蹤 lib.a，即使上面已指定忽略所有的 .a 檔案
!lib.a

# 只忽略根目錄下的 TODO 檔案，不包含子目錄下的 TODO
/TODO

# 忽略 build/ 目錄下所有檔案
build/

# 忽略 doc/notes.txt，但不包含 doc/server/arch.txt
doc/*.txt

# 忽略所有在 doc/ 目錄底下的 .pdf 檔案
doc/**/*.pdf
```

編寫 `.gitignore` 檔案的規則如下：

+ 空白列，或者以`#`開頭的列會被忽略
+ 可以使用標準 Glob 模式
+ `/` 結尾代表目錄
+ 以 `/` 開頭避免路徑傳遞 ( 如果不以斜線開頭，則不管同名檔案或同名資料夾在哪一層都會被忽略 )
+ 以 驚嘆號 ( ! ) 開頭表示將規則反向。

Glob 模式就像是 Shell 所使用的簡化版正則表達式。`*` 匹配零個或多個字元、、`?` 表示匹配一個字元、`[abc]` 匹配中括弧中任一個字元 ( 這裡是 a、b、c )、中括號中的字以連字號連接，表示任何在該範圍的字元，比如：`[0-9]` 表示匹配 0到9、你也可以使用兩個星號來匹配巢狀目錄，`a/**/z` 將會匹配到 a/z、a/b/z、a/b/c/d/z等。

#### 檢視以預存與未預存的檔案

如果想要精確地知道修改了什麼，而不只哪些檔案被修改。可以使用 `git diff` 命令。

##### 當前版本比較

`git diff` 命令可顯示檔案裡那些列被加入或刪除，以補綴(patch)格式呈現。`git diff`命令再不帶任何參數的情況下是比對「工作目錄」與「暫存區」之間的版本，顯示尚未被存入暫存區的內容。如果你想檢視你已經預存接下來將被提交的內容，可以使用`git diff --staged`，這個命令比對的是「預存區」與「最後一次提交」。

`git diff`命令也指定要比較的檔案，使用`git diff 文件名` 指定檔案名稱。舉例來說下面的命令會比較READ.md檔案在工作目錄與暫存區中的差異。

`git diff READ.md`

##### 歷史版本比較

`git diff`不單單只能與單前版本做比較，如果想與某個歷史版本做比較，通過命令 `git diff 歷史版本`可以通過指定索引值、相對位址的方式(在前進後退版本章節有詳細介紹如何指定版本)，當然也可以與指定檔案互相搭配使用。

```
$ git log --oneline
ca82a6d (HEAD -> master, origin/master, origin/HEAD) changed the verison number
085bb3b removed unnecessary test code
a11bef0 first commit

happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/simplegit-progit (master)
$ git diff a11bef0 README

happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/simplegit-progit (master)
$ git diff a11bef0 Rakefile
diff --git a/Rakefile b/Rakefile
index a874b73..8f94139 100644
--- a/Rakefile
+++ b/Rakefile
@@ -5,7 +5,7 @@ require 'rake/gempackagetask'
 spec = Gem::Specification.new do |s|
     s.platform  =   Gem::Platform::RUBY
     s.name      =   "simplegit"
-    s.version   =   "0.1.0"
+    s.version   =   "0.1.1"
     s.author    =   "Scott Chacon"
     s.email     =   "schacon@gmail.com"
     s.summary   =   "A simple gem for using Git in Ruby code."
```

上方執行結果是當前工作目錄下的文件與最初的版本(a11bef0)做比較，README 檔案並無任何變化，Rakefile 檔案的修改以補綴方式呈現。

> 如果你傾向於使用圖形或外部比對工具，有另一種方法可以查看這些差異內容。執行 `git difftool` 取代 `git diff` ，你可以使用軟體工具檢視任何這類型的差異，像是 SourceTree、VsCode等。執行 `git difftool --tool-help` 以查看在你系統上有那些工具可使用。 

#### 提交修改

如果暫存區已經已經建構成你想要的樣子，你可以開始你的提交。記住：任何未暫存的檔案，都不會納入本次提交中。它們的檔案狀態不會有所改變，那些已暫存的且將被提交的檔案，提交後狀態將變為 ( Unmodified )。

`git commit` 命令會將創建一個樹對象紀錄暫存區的內容，並創建一個提交對象描述本次提交，並包含剛創建的樹對象。這麼做會開啟一個你選定的編輯器，可以使用 `git config --global core.editor`命令指定一個你想使用的。

 編輯器會類似顯示以下文字，最近一次`git status`信息以註解方式呈現，最上方有一個空白列供你輸入提交訊息。如果你想對你已經修改的內容得到更明確的提示，可以使用 `git commit -v`，這樣連修改的差異會放入編輯器中。當你關閉編輯器，GIT 會利用這些內容產生新的提交(註解跟差異內容會被刪除)。

```

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Your branch is up-to-date with 'origin/master'.
#
# Changes to be committed:
#	new file:   README
#	modified:   CONTRIBUTING.md
#
~
~
~
".git/COMMIT_EDITMSG" 9L, 283C
```

> `git commit -m <提交訊息>` 可以在不打開編輯器的狀況下提交。
>
> `git commit -a` 會自動幫把以追蹤的檔案加入至暫存區，並開始提交操作，省略 `git add` 的步驟。

#### 查看提交歷史

如果你想查看先前的提交歷史，`git log` 命令可以為你提供幫助。在不指定任何參數的默認情況下，` git log` 會按照時間順序列出所有的提交，這個命令會列出所有SHA-1校驗和、作者姓名和電子郵件地址、提交時間以及提交訊息。

`git log` 有許多選項可以幫助你搜尋提交訊息，下面會介紹幾個常用的選項。

`git log --patch` 或 `git log --p` 選項除了顯示基本訊息之外，還附帶了每次提交的變化。當進行代碼審查時，或者快速瀏覽某個搭檔的提交所帶來的變化時，這個參數就非常有用了。你也可以限制顯示的訊息數量，例如使用 `-2` 選項來顯示最近的兩次提交。

`git log --stat` 選項顯示每次提交的簡略統計訊息。`--stat` 選項在每次提交下面顯示所有被修改的文件、有多少文件被修改以及被修改的文件的那些行被移除或是添加，最後還有一個總結。

`git log --pretty`選項可以使用預設幾個不同的格式展示提交訊息，該選項有一些內建子選項供你選擇。比如 `oneline` 會將每次提交放在一行顯示，在瀏覽大量的提交時非常有用，另外還有`short`、`full`和`fuller`子選項，它們展示訊息的方式格式基本一致，但是詳細程度不一。除了上面幾個默認的子選項，`format` 選項提供了你自訂義訊息顯示格式。這樣的輸出對後期提取分析格外有用，因為你知道輸出的格式不會隨著 Git 的更新而發生改變。

```
$ git log --pretty=format:"%h - %an, %ar : %s"
ca82a6d - Scott Chacon, 6 years ago : changed the version number
085bb3b - Scott Chacon, 6 years ago : removed unnecessary test
a11bef0 - Scott Chacon, 6 years ago : first commit
```

##### --pretty=format 常用的選項

| 選項  | 說明                             |
| :---- | :------------------------------- |
| `%H`  | 提交的完整哈希值                 |
| `%h`  | 提交的簡略哈希值                 |
| `%T`  | 樹的完整哈希值                   |
| `%t`  | 樹的簡略哈希值                   |
| `%P`  | 父提交的完整哈希值               |
| `%p`  | 父提交的簡略哈希值               |
| `%an` | 作者的姓名                       |
| `%ae` | 作者的電子郵件地址               |
| `%ad` | 作者修訂日期                     |
| `%ar` | 作者修訂日期，按多久以前方式顯示 |
| `%cn` | 提交者姓名                       |
| `%ce` | 提交者電子郵件地址               |
| `%cd` | 提交日期                         |
| `%cr` | 提交日期，按多久以前方式顯示     |
| `%s`  | 提交說明                         |

`git log --graph` 選項添加一些ASCII字符串來形象的展示妳的分支、合併歷史。

```console
$ git log --pretty=format:"%h %s" --graph
* 2d3acf9 ignore errors from SIGCHLD on trap
*  5e3ee11 Merge branch 'master' of git://github.com/dustin/grit
|\
| * 420eac9 Added a method for getting the current branch.
* | 30e367c timeout code and tests
* | 5a09431 add timeout protection to grit
* | e1193f8 support for heads with slashes in them
|/
* d6016bc require time for xmlschema
*  11d191e Merge branch 'defunkt' into local
```

##### git log 的常用選項

| 選項              | 說明                                                         |
| ----------------- | ------------------------------------------------------------ |
| `-p`              | 按補丁格式顯示每個提交引入的差異                             |
| `--stat`          | 顯示每次提交的文件修改統計信息                               |
| `--shortstat`     | 只顯示`--stat`中最後的行數修改移除統計訊息                   |
| `--name-only`     | 僅在提交信息後顯示已修改的文件清單                           |
| `--name-status`   | 顯示新增、修改、刪除的文件清單                               |
| `--abbrev-commit` | 僅顯示SHA-1校驗和所有40個字符中的前幾個字符                  |
| `--relative-data` | 使用較短的相對時間而不是完整格式顯示日期                     |
| `--graph`         | 在日誌旁以ASCII圖形顯示分支合併歷史                          |
| `--pretty`        | 使用其他格式顯示歷史提交訊息。可用的選項包括 <br />`oneline`、`short`、`full`、`fuller` 和 `format` |
| `--oneline`       | `--pretty=oneline --abbrev-commit合用的簡寫`                 |

##### 限制輸出長度

除了指定輸出格式的選項之外，`git log`還有許多非常實用的限制輸出長度的選項，也就是只輸出一部分的提交。之前有介紹過`-2`選項，它只會顯示最近的兩條提交訊息，實際上，你可以使用類似`-<n>`的選項，`n`可以是任何整數，表示僅顯示最近的`n`條提交。

另外還有 `--since` 和 `-until` 這種按照時間做限制的選項，例如下面的命令列出最近兩周的所有提交

`git log --since=2.weeks`

該命令可用的格式十分豐富，可以是類似`"2018-01-15"`的具體的某一天，也可以是類似`"2 years 1 day 3 minutes ago"` 的相對日期

還可以過濾出匹配指定條件的提交。`--auth`選項顯示指定作者的提交、`--grep`選項搜索提交說明中的關鍵字。

> 你可以指定多個 `--auth` 和 `--grep` 搜索條件，這樣只會輸出任意匹配 `--auth` 模式和 `--grep` 模式的提交。然而，如果你添加了 `--all-match` 選項，則只會輸出**所有**匹配 `--grep` 模式的提交。

另外一個非常有用的過濾器選項是`-S`，它接受一個字符串參數，並且只會顯示那些修改內容（添加、刪除）包括了該字符串的提交。假設你想找出添加或刪除了對某一特定函數的提交，可以使用以下命令

` git log -S function_name`

##### 過濾 git log 輸出的常用選項

| 選項                    | 說明                                     |
| ----------------------- | ---------------------------------------- |
| `-<n>`                  | 僅顯示最近的 n 條提交                    |
| `--since` 或 `--after`  | 僅顯示指定時間之後的提交                 |
| `--until` 或 `--before` | 僅顯示指定時間之前的提交                 |
| `--auth`                | 僅顯示作者匹配指定字符串的提交           |
| `--comitter`            | 僅顯示提交者匹配指定字符串的提交         |
| `--gerp`                | 僅顯示提交說明中包含指定字符串的提交     |
| `-S`                    | 僅顯示添加或刪除內容匹配指定字符串的提交 |

> 按照你代碼倉庫的工作流程，紀錄中可能有為數不少的合併提交，它們所包含的訊息通常不多。為了避免顯示的合併提交訊息干擾查看歷史提交，可以為`log`加上`--no-merges`選項

#### 刪除檔案

要從 GIT 刪除一個檔案，你需要將它從以追蹤檔案中移除。`git rm` 命令可以完成這個工作，它會將檔案從工作目錄中刪除，並且將本次修改添加至暫存區 ( 刪除檔案是一種修改 )，下一次提交時，該檔案將會消失且不再被追蹤。

```
$ git rm PROJECTS.md
rm 'PROJECTS.md'
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    deleted:    PROJECTS.md
```

如果你已經修改了檔案並且加入暫存區，你必須使用 `-f`選項才能強制將它移除，這是一種為了避免已記錄的快照意外被移除後再也無法使用 GIT 復原的機制。

另一個技巧是你想保留工作目錄的檔案，但是想取消 GIT 對它的追蹤，`--cached` 選項可以做到這件事。

```
git rm --chched README
```

> 你可以將「檔案」、「目錄」、「file-glob 模式」做為參數傳給 `git rm` 命令。注意：`* `前必須有跳脫字元 ( \ )

#### 移動與從命名檔案

如果你想移動或重命名檔案，可以執行以下命令。

```
git rm file_from file_to
```

以下為重命名檔案範例

```
$ git mv README.md README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README
```

其實，`git mv`相當於執行以下命令

```
$ mv README.md README
$ git rm README.md
$ git add README
```

#### 前進後退版本

##### 前進後退版本的本質

`master` 是`git init`指令預設創建的分支名，`HEAD`和`master`其實都是指針，`HEAD`所指向的是你當前的位置，上面訊息其實就是在說明當前的`HEAD`指向`master`分支。控制當前的版本，其實就是操作`HEAD`分支所指向的位置。

```
$ git log --oneline
ca82a6d (HEAD -> master, origin/master, origin/HEAD) changed the verison number
085bb3b removed unnecessary test code
a11bef0 first commit
```

`git reset`指令提供可以接收3種值來操作`HEAD`指針的位置。

+ 索引值(推薦)

  每次提交 Git 都會使用SHA-1算法計算該次提交的哈希值，通過哈希值可以指定想將`HEAD`移動至哪次提交。

  ```
  $ git log --oneline
  ca82a6d (HEAD -> master, origin/master, origin/HEAD) changed the verison number
  085bb3b removed unnecessary test code
  a11bef0 first commit
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/simplegit-progit (master)
  $ git reset --hard a11bef0
  HEAD is now at a11bef0 first commit
  ```

  注意不需要指定完整的哈希值，只需取前7碼即可。這裡使用了`--hard`選項，這是`git reset`模式的指定，稍後會詳細解釋。

+ 使用`^`符號：只能後退版本

  一個`^`符號代表後退一個版本，可以給定多個`^`來後退 n 個版本，如果想後退20個版本就給定20個`^`，這種方式有點不方便，後續介紹的第三種方式是對其改進。

  ```
  $ git log --oneline
  ca82a6d (HEAD -> master, origin/master, origin/HEAD) changed the verison number
  085bb3b removed unnecessary test code
  a11bef0 first commit
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/simplegit-progit (master)
  $ git reset --hard HEAD^
  HEAD is now at 085bb3b removed unnecessary test code
  ```

  `git reset --hard HEAD^` 退回到當前版本的前一個版本，因為`HEAD`指針所指向的位置是當前版本。

+ 使用`~`符號：只能後退版本

  `~<n>`方式通過指定n的方式，後退 n 個版本。

  ```
  $ git log --oneline
  ca82a6d (HEAD -> master, origin/master, origin/HEAD) changed the verison number
  085bb3b removed unnecessary test code
  a11bef0 first commit
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/simplegit-progit (master)
  $ git reset --hard HEAD~2
  HEAD is now at a11bef0 first commit
  ```

##### 查看操作歷史紀錄

`git log`命令只能顯示的後當前版本以後的版本。當你通過`git reset`命令後退版本後，你想找到提交訊息的哈希值來前進到某個版本，你會發現`git log`沒有未來得版本訊息。這時透過`git reflog`命令查看操作歷史紀錄，這裡記錄著你所有的操作產生的哈希值，通過指定這個哈希值的方式，你又可以回來到執行`git reset`命令前的版本

> 在GIT 版本控制的特色中所提到的只要你有 commit，那麼就一定可以找回來，就是指通過`git reflog`的輔助，可以在任一版本間切換。

```
$ git reflog
a11bef0 (HEAD -> master) HEAD@{0}: reset: moving to HEAD~2
ca82a6d (origin/master, origin/HEAD) HEAD@{1}: reset: moving to ca82a6d
085bb3b HEAD@{2}: reset: moving to HEAD^
ca82a6d (origin/master, origin/HEAD) HEAD@{3}: reset: moving to ca82a6d
a11bef0 (HEAD -> master) HEAD@{4}: reset: moving to HEAD~1
085bb3b HEAD@{5}: reset: moving to HEAD^
ca82a6d (origin/master, origin/HEAD) HEAD@{6}: reset: moving to ca82a6d
a11bef0 (HEAD -> master) HEAD@{7}: reset: moving to a11bef0
ca82a6d (origin/master, origin/HEAD) HEAD@{8}: reset: moving to ca82a6d
a11bef0 (HEAD -> master) HEAD@{9}: reset: moving to a11bef0
a11bef0 (HEAD -> master) HEAD@{10}: reset: moving to a11bef0
ca82a6d (origin/master, origin/HEAD) HEAD@{11}: reset: moving to ca82a6d
a11bef0 (HEAD -> master) HEAD@{12}: reset: moving to a11bef0
ca82a6d (origin/master, origin/HEAD) HEAD@{13}: clone: from https://github.com/schacon/simplegit-progit
```

##### soft、mixed、hard 選項

在講這三個`git reset`的選項之前，回顧一下 GIT 的工作流程，GIT 將其分為三個部分，分別是工作目錄(Working Directory)、暫存區(Staged/Index)、資源庫(Repository)。當有檔案被修改後，工作目錄與暫存區及資源庫的訊息會不一致，此時 GIT 就判斷該檔案狀態為`modified (Unstaged files)` 。執行 `git add`命令將檔案新增至暫存區，此時工作目錄與暫存區的訊息一致且與資源庫不一致，此時 GIT 就判斷該檔案的狀態為`Staged files`。執行`git commit`將提交至資源庫後，此時三個工作區的資訊又回到最初的一致狀態。

明白了前面 GIT 的工作流程後，`git reset`各參數的的差異就只是資料恢復的範圍，決定是否將工作目錄或暫存區中的資料一起 reset 成與指定節點的資源庫資訊一致。

`--soft` 命令不會變更工作目錄的文件，**從當前版本到目標版本間的差異變更集會被放置到暫存區**， 未追蹤和未暫存的檔案會維持原樣。此時工作目錄與暫存區是一致的。具體使用情況是當我們想合併**當前節點**到 **reset 目標節點**間不具有太大意義的提交紀錄。

`--mixed`命令不會變更工作目錄的文件，**從當前版到到目標版本的的差異變更集會被放置到工作目錄**。暫存區與資源庫會被 reset 為與目標版本一致。已暫存但是未提交的修改也會被存放至工作目錄中。max子命令是預設`git reset` 的行為。具體使用場景是當想要把所有已暫存的檔案回復為未暫存狀態，`git reset --mixed HEAD` 命令會將資源庫回復到當前版本，同時將已暫存的檔案列為未暫存狀態。

`--hard`命令會讓強制工作目錄、暫存區、資源庫與目標版本一致，所以不會保留當前版本到目標版本的提交，當前版本未追蹤和未暫存的檔案也會丟失。

#### 分支

幾乎所有的版本控制系統都以某種形式支持分支。但是在很多版本控制系統中，這是一個略為低效的過程，常常需要完全創建一個源代碼目錄的副本，對於大型項目，這樣的過程會耗費很多時間。

GIT 處理分支的方式與眾多版本控制系統有著巨大的差異，輕量化設計使創建分支是如此快速、分支之間的切換操作相當便捷，這也是 GIT 可以在版本控制系統中脫穎而出的原因。

分支的隔離性很好的保證系統的穩定性。假設有新的功能需要開發時，會創建對應的功能分支，直到新功能開發完成並穩定運行時，才會將該功能分支合併進主分支。一來保證了主分支的穩定性，也為新功能的開發提供了試錯空間，不需要擔心錯誤會影響到主分支。

分支也支持並行開發，假設全部功能都在主線上開發，所有的代碼都集中在主線上，單個功能的錯誤會影響其他功能的開發進度，那是我們不樂見的。

+ 分支的好處
  + 多個功能並行開發，提高開發效率
  + 分支的獨立性，避免各功能穩定性受到影響

##### 分支簡介

在進行提交操作時，GIT 會保存一個提交對象(commit object)。知道了 GIT 保存數據的方式，我們可以很自然的想到，該提交對象保存一個指向暫存內容的快照、作者的姓名與電子信箱地址、提交時輸入的訊息及指向其父對象的指針。首次提交所產生的提交對象沒有父對象，普通提交產生的提交對象有一個父對象，由多個分支合併產生的提交對象有多個父對象。

GIT 的分支，其實本質上僅僅是指向提交對象的可變指針。GIT 的默認分支名稱是 `master` 。如果當前在 `master` 分支，那麼 `master`分支會在每次提交時自動向前移動。

> `master`分支並不是一個特殊分支。它與其他分支完全沒有區別。之所以每個倉庫都有 `master` 分支，是因為 `git init`命令默認創建它，並且大多數人都懶得去改動它。

##### 分支創建

創建分支就是為你創建一個可移動的新指針。例如，創建一個 testing分支，需要使用 `git branch` 命令

```
git branch testing
```

這會在當前提交對象上創建一個 `testing` 指針。請注意現在還在 `master` 分支上，上面的命令只是**創建**一個新分支，並不會自動切換到新分支。

現在 `master` 和 `testing` 分支都指向同一個提交對象。那麼 GIT 又是如何確定我們在哪個分支上呢？GIT 有一個名為 `HEAD` 的特殊分支，它是一個指針，指向當前所在的本地分支(可以將 HEAD 想做是當前分支的別名)。

##### 分支切換

要切換到一個已存在的分支，使用 `git checkout` 命令

```
git checkout testing
```

這樣 `HEAD` 就指向 `testing` 分支了

如果你在提交一次，你會發現 `HEAD` 所指的分支向前移動了，而 `master` 分支還停留運行 `git checkout` 時所指的提交對象

```
$ git log --oneline
4cf23ee (HEAD -> testing) make a change
ca82a6d (origin/master, origin/HEAD, master) changed the verison number
085bb3b removed unnecessary test code
a11bef0 first commit
```

> 切換分支會改變你工作目錄中的文件。如果你是切換到一個較舊的分支，你的工作目錄會恢復到該分支最後一次提交時的樣子。如果 GIT 不能乾淨俐落的完成切換任務，它將禁止切換分支。

此時如果切回`master`分支，並且做一次提交。現在項目中的歷史已經產生分叉，兩次提交針對不同的分支，你可以在不同分支間切換，並在時機成熟時將他們合併起來。

可以通過 `git log --oneline --graph --all` 命令查看分支分叉的情況

```
$ git log --oneline --graph --all
* b9e99b8 (HEAD -> master) make a change
| * 4cf23ee (testing) make a change
|/
* ca82a6d (origin/master, origin/HEAD) changed the verison number
* 085bb3b removed unnecessary test code
* a11bef0 first commit
```

> 通常我們會在創建一個新分支後立即切換過去，這可以用 `git checkout -b <newbranchname>`一條命令搞定

##### 合併分支

`git merge`命令可以將指定分支的內容合併進另一個分支中，例如我們想合併`master`分支與`hotfix`分支，首先必須好誰是被合併的分支，誰是合併分支。假設我們是要在`master`分支上併入`hotfix`分支的內容，那被合併分支就是`hotfix`。

```
$ git checkout master
$ git merge hotfix
Updating f42c576..3a0874c
Fast-forward
 index.html | 2 ++
 1 file changed, 2 insertions(+)
```

可以注意到 GIT 訊息中的「fast-forward」，當你想要合併的分支`hotfix`所指向的提交，是你當前分支`master`的直接後繼，因此 GIT 會直接將指針向前移動。換句話說，當你試圖合併兩個分支時，如果順著一個分支走下去能夠直接到達另一個分支，那麼 GIT 在合併兩者時，會簡單的將指針往前推進，因為這種情況下合併沒有需要解決的分歧，這就叫快進(fast-forward)。

如果合併分支不是被合併分支的直接祖先，那麼 GIT 不得做一些額外的工作。出現這種情況，GIT 會使用兩分支的末端所指的快照以及兩分支的共同祖先，做一個三方合併。

如果三方合併順利，和快進合併不同的是，GIT 會將此事三方合併的結果做一個新的快照並且自動創建一次新的提交指向它。這被稱為合併提交，特別之處它不只有一個父提交。

##### 刪除分支

如果創建的分支使命已經結束，可以使用`git branch -d <分支名>`來刪除不在需要的分支。

##### 解決合併衝突

三方合併不一定都是順利的，當兩分支都修改到同一支檔案，此時會產生衝突，GIT 等待你需解決完衝突後才能繼續合併作業。

```
happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/simplegit-progit (master)
$ git checkout master
Already on 'master'
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/simplegit-progit (master)
$ git merge testing
CONFLICT (add/add): Merge conflict in test.rb
Auto-merging test.rb
Automatic merge failed; fix conflicts and then commit the result.

happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/simplegit-progit (master|MERGING)
```

你可以在合併衝突後的任意時刻使用`git status`命令查看那些因包含合併衝突而處於未合併(unmerged)狀態的文件。

```
$ git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both added:      test.rb

no changes added to commit (use "git add" and/or "git commit -a")

```

GIT 會在有衝突的文件加入標準的衝突解決記號，這樣你可以打開這些包含衝突的文件，然後手動解決衝突。出現衝突的文建會包含一些特殊區段，看起來像下面這個樣子：

```
<<<<<<< HEAD
Hello Java
=======
Hello World
>>>>>>> testing
```

#### SSH登入

使用 HTTP 協定來訪問遠程庫，每次`push`都會要求你填入相應的帳號密碼(W10會自動將憑證記住，所以不用重登)。如果覺得每次`push`都必須重新登入太過麻煩，那麼你可以考慮使用 SSH 協定來訪問你的遠程庫。

要想不用填寫登入訊息，首先必須創建相應的文件。下面列出操作步驟

1. 進入使用者目錄 `cd ~`
2. 刪除舊有的 .ssh 目錄 `rm -rvf .ssh`
3. 運行命令生成 .ssh 目錄 `ssh-keygen -t rsa -C github帳戶郵件地址`
4. 進入 .ssh 目錄查看文件列表 `cd ssh` `ls-lf`
5. 查看 id_rsa.pub 文件內容
6. 複製 id_rsa.pub 文件內容，登入 github 帳戶 -> 進入Setting -> SSH and GPG keys
7. New SSH Key
8. 輸入複製的 id_ras.pub 內容，並為該 SSH Key 命名
9. 完成
10. 之後只要到你的本地庫，使用`git remote add ` 添加 SSH 連接，就可以透過該 SSH 連接與遠程庫交互。

> 每個電腦的使用者只能設定一個SSH

#### Eclipse 中使用 GIT 版本控制

Eclipse 其實就內置了 GIT 的版本控制插件

操作步驟

1. 專案右鍵 -> Team -> Share Project -> GIT
2.  use or create repository in parent folder or project 打勾
3. 勾選要創建 git repository 的專案，點擊 Create Repository 按鈕
4. 點擊 Finsh 按鈕

完成後 GIT 就會管理你的專案，Team下可選的項目也會有所不同。在 Preferences 中 GIT -> configuration -> 也能看到專案級別的 `git config` 設定。

##### 檔案狀態圖標

Preferences 中搜尋 GIT -> Label Decorations 中可以預覽 GIT 各種檔案狀態所顯示的圖標，由於各版本圖標可能有所不同，所以這裡只列出圖標名。

+ tracked 表示被追蹤的檔案
+ untracked 表示未被追蹤的檔案
+ ignored 表示被忽略的檔案
+ dirty 表示異動的檔案
+ staged 表示已暫存檔案
+ partially-staged 表示已暫存但是又有異動的檔案
+ added 表示新增的檔案
+ removed 表示刪除的檔案
+ conflict 表示發生衝突的檔案

##### 忽略 Eclipse 特定文件

下面列出的是Eclipse 為了管理工程所創建的文件，在開始使用GIT之前，需要添加至 `.gitignore` 中，我們並不希望將這些與開發無關文件列入 GIT 的管理。其實 GITHUB 的官網有提供 `.gitignore` 的範本，可以根據所開發的語言去選擇適合的範本，並依據專案具體需求修改。[gitignore範本](https://github.com/github/gitignore)

+ .classpath 文件
+ .project 文件
+ .setting 文件

`.gitignore`可以設定層級，採就近原則，當專案下有`.gitignore`文件，就會忽略全局的設定，這與`.gitconfig`是相同的。

下面列出設定全局`.gitignore`步驟

1. 切換至使用者根目錄 `cd ~`

2. 創建 `.gitignore` 結尾文件，名稱可以自訂副檔名符合即可，也可以依據 Github 範本名方式。

3. 編輯使用者根目錄下的 `.gitconfig`，添加以下內容。假設文件名為 Java.gitignore

   ```
   [core]
   	excludesfile=C:/Users/James/Java.gitignore
   ```

   > 需要注意路徑書寫必須使用「/」，而不是「\」。
4. 重啟 Eclipse 會發現檔案被忽略