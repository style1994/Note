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

<img src="images/GIT/image-20210326120122309.png" alt="image-20210326120122309" style="zoom:50%;" />

<img src="images/GIT/image-20210326120427742.png" alt="image-20210326120427742" style="zoom:50%;" />

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

![image-20210326120122309](GIT/image-20210326120122309.png)

提交對象中的 parent 記錄該提交對象的父對象，如果版本的內容不源自提交，那麼 parent 為空，通常是第一個提交，該提交也被稱為「root commit」。

![image-20210326120427742](GIT/image-20210326120427742.png)

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

##### pretty=format選項格式

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

##### 常用選項

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

##### 過濾歷史訊息輸出常用選項

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

#### 撤銷

+ 撤銷暫存區的暫存

  ```
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git status
  On branch master
  Changes to be committed:
    (use "git restore --staged <file>..." to unstage)
          modified:   README
  ```

  通過 `git status` 命令查看檔案狀態時就有提示你撤銷操作命令，通過 `git restore --staged <file>` 來撤銷棧存操作。

  在撤銷前可以通過`git ls-files -s` 與 `git cat-file -p` 查看暫存區中 README的內容。

  ```
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git ls-files -s
  100644 3fbbfe59475356d155ae88d6d052ad213c706fde 0       README
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git cat-file -p 3fbbfe59475356d155ae88d6d052ad213c706fde
  Hello World
  Hello Java
  ```

  通過 `git restore --stage ` 命令撤銷暫存

  ```
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git restore --staged README
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git ls-files -s
  100644 557db03de997c86a4a028e1ebd3a1ceb225be238 0       README
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git cat-file -p 557db03de997c86a4a028e1ebd3a1ceb225be238
  Hello World
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git status
  On branch master
  Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git restore <file>..." to discard changes in working directory)
          modified:   README
  
  no changes added to commit (use "git add" and/or "git commit -a")
  ```

+ 撤銷工作目錄檔案的修改

  ```
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git status
  On branch master
  Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git restore <file>..." to discard changes in working directory)
          modified:   README
  
  no changes added to commit (use "git add" and/or "git commit -a")
  
  ```

  通過 `git status` 命令查看檔案狀態時就有提示你撤銷操作命令，通過 `git restore <file>` 來撤銷工作目錄檔案的修改。

  ```
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ cat README
  Hello World
  Hello Java
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ git restore README
  
  happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/10 (master)
  $ cat README
  Hello World
  ```

+ 撤銷自己的提交

  當你提交後才發現提交訊息寫錯，或是漏上檔案。可以使用帶有 `--amend`選項的`git commit`。這個命令會將暫存區的文件提交，如果自上次提交後沒有任何修改，那麼這條命令只有修改提交訊息。

  如果漏上了某些文件，可以這樣操作

  ```
  $ git commit -m 'initial commit'
  $ git add forgotten_file
  $ git commit --amend
  ```

  最終你只會有一個提交——第二次提交將代替第一次提交的結果。

  > 當你在修補最後的提交時，並不是通過用改進後的提交**原位替換**掉舊有提交的方式來修復的，理解這一點非常重要。**從效果上來說，就像是舊有的提交從未存在過一樣，實際上它只是不會出現在倉庫的歷史中。並沒有刪除上一個提交對象。**修補提交最明顯的價值是可以稍微改進你最後的提交，而不會讓“啊，忘了添加一個文件”或者“小修補，修正筆誤”這種提交信息弄亂你的倉庫歷史。

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

##### 查看分支

`git branch` 如果不加任何選項，會顯示一個分支列表。如果想要看更詳細的訊息。可以加上`-v`選項，會列出各分支的所指向的提交對象。`-vv`選項可以查看到每個分支所設置的遠端追蹤分支。

##### 分支創建

創建分支就是為你創建一個可移動的新指針。例如，創建一個 testing分支，需要使用 `git branch` 命令

```
git branch testing
```

這會在當前提交對象上創建一個 `testing` 指針。請注意現在還在 `master` 分支上，上面的命令只是**創建**一個新分支，並不會自動切換到新分支。

如果你不想在當前的位置創建新分支，`git branch` 命令也提供，指定提交對象哈希值的方式，可以創建一個指向該提交對象的分支。

```
git branch 分支名 提交對象哈希
```

> 如果你想在創建分支後，馬上切換到該分支。可以使用`git checkout -b <new branch>`。
>
> 2.23版後`checkout`命令與切換分支有關的功能由`switch`接手，所以`git switch -c <new branch> [提交對象哈希值]`與上面`checkout`命令效果一致(提交對象哈希是選用的)。`-C`選項會強制覆蓋同分支名。

##### HEAD分支

當你創建了許多分支後，你可能會有以下疑問。 GIT 又是如何確定我們在哪個分支上呢？GIT 有一個名為 `HEAD` 的特殊分支，它是一個指針，指向當前所在的本地分支(可以將 HEAD 想做是當前分支的別名)。

##### 分支切換

要切換到一個已存在的分支，使用 `git checkout` 命令。**2.2.3版引入了兩個新命令來取代`checkout`命令，`checkout`命令承擔了太多的職責，以至於讓人在使用時感到困惑。其中切換分支的的職責由`git switch`接手。**

```
git checkout testing
或
git switch testing
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

如果創建的分支使命已經結束，可以使用`git branch -d <分支名>`來刪除不在需要的分支。**你無法再當前分支上刪除當前分支，你必須切換到別的分支，在執行刪除操作。**

> 未合併的分支 GIT 無法使用 `-d` 直接刪除，這是為了防止你誤刪尚未合併分支，需要使用`-D`選項強制刪除。

##### 重命名分支

如果對分支的名稱不滿意，`git branch -m` 可以重新命名當前分支名。如果分支名稱已存在，重命名操作會失敗。

> `git branch -M` 是強制重命名，不管分支名是否已存在。如果已存在會直接覆蓋。(包含檔案)

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

##### 查看合併與未合併分支

`--merged` 和 `--no-merged` 選項可以查看合併或未合併到指定分支的分支列表。

```
git branch --merged <分支>
git branch --no-merged <分支>
```

如果沒有指定分支，默認會是當前分支。

#### 遠程分支

遠程引用是對遠程倉庫的引用(指針)，包括分支、標籤等等。可以通過`git ls-remote <remote>`來顯示的獲得遠程引用的完整列表。或者通過`git remote show <remote>`獲得更多遠程分支的更多訊息，然而，一個更常見的做法是利用「遠程跟蹤分支」。

**遠程跟蹤分支是遠程分支狀態的引用**。它們是你無法移動的指針。一旦進行了`git fetch`獲取遠程倉庫數據，GIT 就會為你移動它們以精準反映遠程倉庫的狀態。請將它們當作書籤，目的是提醒你最後一次獲取數據時遠程分支在遠程倉庫的位置。

遠程跟蹤分支以<remote>/<branch>形式命名。例如：你想要查看你最後一次與遠程倉庫`origin`通信時`master`分支的狀態，你可以查看`origin/master`這個遠程跟蹤分支。

> 遠程跟蹤分支是本地分支與遠程分支溝通的「媒介」。它們三者是不同的分支，遠程分支不等於遠程跟蹤分支，必須要釐清清楚它們的關係。

##### 推送本地分支

如果想和團隊成員分享你的本地分支，那麼需要將本地分支推送至一個有寫入權限的遠程倉庫上。本地分支並不會自動與遠程倉庫同步，你必須顯式的推送你想要分享的內容。

如果本地的`serverfix`分支要跟想給團隊成員，那麼你可以運行 `git push <remote> <branch>`將本地分支推送至遠端倉庫。假設遠端倉庫簡稱為 origin 那麼以下操作將分享你的 serverfix 分支

```
git push origin serverfix
```

這裡有些工作被簡化了，GIT 自動將 sercerfix 分支名子展開為 `refs/heads/serverfix:refs/heads/serverfix`，這表示推送本地的`serverfix`分支來更新遠程的`serverfix`分支。你也可以運行 `git push <remote> localbranch:remotebranch`，當本地分支與遠程分支不同名稱時，可以這樣推送。或是你初次推送本地分支時並不希望遠端倉庫的遠程分支名稱與本地分支相同，也可以採用這種方式做初次推送。

下一次團隊成員執行`git fetch`操作時，會在本地生成`origin/serverfix`的遠程跟蹤分支，指向遠端倉庫`origin`上的`serverfix`遠程分支。

需要注意的是，執行`git fetch`操作不會在本地庫生成對應的本地分支。以上面的例子為例：不會自動幫你創建本地分支`serverfix`，只會有一個不可修改的`origin/serfix`遠程跟蹤分支。	

##### 合併遠程分支數據

既然`git fetch`不會自動幫你合併，但是該操作已經將數據獲取至遠程跟蹤分支上。所以使用`git merge`操作將遠程跟蹤分支所對應的遠程分支數據合併到本地分支上。

```
git merge origin/serfix
```

##### 跟蹤分支

當執行`git push`或`git pull`進行拉取或推送數據至遠端時，都必須明確指定是遠端的哪個分支。想要不指定又能推送或拉取成功，就必須讓本地分支與遠程跟蹤分支做一個綁定，這樣GIT才能知道是要拉取或推送的目標遠端分支，綁定過後的本地分支也被稱為「跟蹤分支」。

當克隆一個倉庫時，通常會自動創建一個`origin/master`的`master`分支。然而如果你願意的話可以設置其他的跟蹤分支，或是在其他倉庫的跟蹤分支，又或者不跟蹤`origin/master`分支。

`git checkout -b <branch> <remote>/<branch>` 該命令會創建分支、切換分支、設置創建的分支綁定遠程跟蹤分支，且該新分支與遠程跟蹤分支指向同一個提交對象。

```
$ git checkout -b serverfix origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

因為上面的命令太常使用了，如果沒有額外指定的新分支名稱需求，那可以使用以下便捷命令，會創建一個與遠程分支同名的本地分支。

`git branck --track <remote>/<branck>`

```
$ git checkout --track origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

便捷命令還有一個捷徑，當你嘗試檢出的分支不存在且剛好只有一個名子與之匹配的遠程分支，那麼GIT 就會為你建立跟蹤分支。

```
$ git checkout serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

設置的綁定可以修改，你可以在任意時間使用帶有`-u`或`--set-upstream-to`選項的`git branch`命令顯示設置。

```
$ git branch -u origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
```

##### 查看詳細分支訊息

若你想查看當前所有跟蹤分支，可以使用`git branch`的`-vv`選項，這會將本地分支列出來並且包含更多的訊息。如每一個分支正在跟蹤的哪個遠程分支與本地是否有領先、落後或是都有。

```
$ git branch -vv
  iss53     7e424c3 [origin/iss53: ahead 2] forgot the brackets
  master    1ae2a45 [origin/master] deploying index fix
* serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] this should do it
  testing   5ea463a trying something new
```

`ahead 2` 表示還有兩個提交沒有推送到遠程倉庫。本地`master`分支與`origin/master`遠程跟蹤分支現在是一致的。`ahead 3 behind 1` 表示 `serverfix`正在跟蹤的 `teamoone/server-fix-good`分支並且領先3落後1。代表遠程倉庫上有一次提交還沒有合併入本地`serverfix`分支且本地還有3次提交還沒有推送。

需要注意的是這些資訊都是根據你最後一次與遠程倉庫拉取的數據做比對。這個命令沒有連接遠程倉庫，如果想要統計最新資訊，可以這樣做。

```
git fetch --all
git branch -vv
```

##### 拉取

`git pull`命令在大多數情況下它的含意是 `git fetch` 緊接著一個 `git merge` 命令。如果有設置好跟蹤分支，`git pull` 會查找與當前所跟蹤的遠程倉庫分支，從遠程倉庫抓取數據然後嘗試併入那個遠程分支。

由於`git pull`魔法經常讓人困惑所以通常顯式的使用`fetch`與`merge`命令會更好一些。

##### 刪除遠程分支

假設你與團隊成員通過遠程分支完成所有工作了，並且將該遠程分支併入遠程倉庫的主分支。這時就可以運行帶有`--delete`選項的`git push`命令來刪除遠端分支。

```
$ git push origin --delete serverfix
To https://github.com/schacon/simplegit
 - [deleted]         serverfix
```

基本上這個命令做的只是從服務器上移除這個指針。Git 服務器通常會保留數據一段時間直到垃圾回收運行，所以如果不小心刪除掉了，通常是很容易恢復的。

刪除遠程倉庫的分支後，本地倉庫內的遠程跟蹤分支還未刪除，所以運行以下命令刪除不在需要的遠程跟蹤分支。

```
git push prune origin --dry-run //列出仍在遠程跟蹤但是遠程已被刪除的無用分支
git remote prune origin //清除上面命令列出的遠程跟蹤分支
```



#### 變基(rebase)

在 GIT 中整合來自不同分支的修改主要有兩種方式：`merge`與`rebase`。

之前介紹過，整合分支最容易的是 `merge` 命令，會把兩個最新分支的快照(C3、C4)以及兩者最近的共同祖(C2)先進行三方合併。

![image-20210331143908782](images/GIT/image-20210331143908782.png)

其實，還有一種方法：你可以提取在C4中引入的補丁和修改，然後C3的基礎上應用一次。在GIT中，這種操作就叫做變基( rebase )。你可以使用`rebase`命令將提交到某一個分支上所有修改都移至另一個分支上，就好像「重新撥放」一樣。

這個例子中，你可以切換到`experiment`分支，然後將它變基到`master`分支上。

```
$ git checkout experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```

它的原理是首先找到兩個分支(當前分支 `experiment`、變基操作的目標基底分支 `master`)的共同祖先C2，然後對比當前分支相較於該祖先的歷次提交，提取相應的修改並存為臨時文件，然後將當前分支指向目標基底C3，最後以此將之前另存為臨時文件的修改依序應用。

![image-20210331151042286](images/GIT/image-20210331151042286.png)

現在回到`master`分支，進行一次快進合併。

```
$ git checkout master
$ git merge experiment
```

![image-20210331151232513](images/GIT/image-20210331151232513.png)

此時，`C4'`指向的快照就和`C5`指向的快照一模一樣了。這兩種整合方法的最終結果沒有任何區別，但是變基使得提交歷史更加整潔。你在查看一個經過變基的分支的歷史記錄時會發現，儘管實際的開發工作是並行的，但它們看上去就像是串行的一樣，提交歷史是一條直線沒有分叉。

一般我們這樣做的目的是為了確保在向遠程分支推送時能保持提交歷史的整潔——例如向某個其他人維護的項目貢獻代碼時。在這種情況下，你首先在自己的分支裡進行開發，當開發完成時你需要先將你的代碼變基到`origin/master`上，然後再向主項目提交修改。這樣的話，該項目的維護者就不再需要進行整合工作，只需要快進合併便可。

請注意，無論是通過變基，還是通過三方合併，整合的最終結果所指向的快照始終是一樣的，只不過提交歷史不同罷了。變基是將一系列提交按照原有次序依次應用到另一分支上，而合併是把最終結果合在一起。

> 使用`git rebase <basebranch> <topicbranch>`命令可以直接將主題分支（即本例中的`server`）變基到目標分支（即`master`）上。這樣做能省去你先切換到`server`分支，再對其執行變基命令的多個步驟。

##### 變基的風險

呃，奇妙的變基也並非完美無缺，要用它得遵守一條準則：

**`如果提交存在於你的倉庫之外，而別人可能基於這些提交進行開發，那麼不要執行變基。`**

變基操作的實質是丟棄一些現有的提交，然後相應地新建一些內容一樣但實際上不同的提交。如果你已經將提交推送至某個倉庫，而其他人也已經從該倉庫拉取提交並進行了後續工作，此時，如果你用`git rebase`命令重新整理了提交並再次推送，你的同伴因此將不得不再次將他們手頭的工作與你的提交進行整合，如果接下來你還要拉取並整合他們修改過的提交，事情就會變得一團糟。

##### 變基 vs 合併

你一定會想問，到底哪種方式更好。在回答這個問題之前，讓我們退後一步，想討論一下提交歷史到底意味著什麼。

有一種觀點認為，倉庫的提交歷史即是**記錄實際發生過什麼**。它是針對歷史的文檔，本身就有價值，不能亂改。從這個角度看來，改變提交歷史是一種褻瀆，你使用*謊言*掩蓋了實際發生過的事情。如果由合併產生的提交歷史是一團糟怎麼辦？既然事實就是如此，那麼這些痕跡就應該被保留下來，讓後人能夠查閱。

另一種觀點則正好相反，他們認為提交歷史是**項目過程中發生的事**。沒人會出版一本書的第一版草稿，軟件維護手冊也是需要反復修訂才能方便使用。持這一觀點的人會使用`rebase`及`filter-branch`等工具來編寫故事，怎麼方便後來的讀者就怎麼寫。

現在，讓我們回到之前的問題上來，到底合併還是變基好？希望你能明白，這並沒有一個簡單的答案。Git 是一個非常強大的工具，它允許你對提交歷史做許多事情，但每個團隊、每個項目對此的需求並不相同。既然你已經分別學習了兩者的用法，相信你能夠根據實際情況作出明智的選擇。

**總的原則是，只對尚未推送或分享給別人的本地修改執行變基操作清理歷史， 從不對已推送至別處的提交執行變基操作，這樣，你才能享受到兩種方式帶來的便利。**

#### 別名(alias)

 GIT 中有些命令使用頻繁，命令的選項又多，如果每次使用都要寫這麼一大串的命令，想必不是我們想見的。GIT  提供了別名功能，你可以為常用的命令取一個短一點的別名。在使用該命令時，就可以透過別名來使用該命令。

```
git config --global alias.st 'status'
```

別名也是記錄在 `gitconfig` 設定檔中，根據具體的需求設定合適的層級。

#### GIT 儲藏

有時，當你在當前分支工作一段時間後，修改了許多文件，此時想切去別的分支做點事情，可是你又不想為了等等就要再回來的繼續做的事情做一個提交，`git stash` 命令可以幫你解決問題。**它會將未完成的修改保存到一個棧上，而你可以在任何時候重新應用這些改動。**

+ `git stash list` 查看所有儲藏。

+ `git stash` 或 `git stash push` 保存當前分支的所有追蹤文件的修改與已暫存的改動

  + `--include-untracked` 或 `-u`：儲藏時包含未追蹤的檔案，但是仍然不會包含忽略的文件。如果要包含忽略的文件，請使用`--all`選項
  + `--patch`：使用該選項 GIT 不會儲藏所有改動過的東西，而是會交互詢問你那些檔案室要被儲藏的。

+ `git stash apply <儲藏>` 應用一個儲藏，如果你不指定一個儲藏，GIT 默認使用最近的儲藏。

  + `--index`：應用時，如果想要讓原來暫存的內容還是在暫存區，加上該選項

+ `git stash pop` 應用棧上最新的儲藏，並且移除它。

+ `git stash drop <儲藏>` 移除指定儲藏。

+ `git stash branch <新分支名> <儲藏>`

  如果儲藏工作一段時間後，然後你繼續在儲藏的分支上繼續工作，那麼在應用儲藏到現在分支時可能會有所衝突。`git stash branch` 命令會以你指定的分支名稱創建一個新分支，在儲藏操作的那個提交的基礎上。並切換到該新分支，應用儲藏的修改。如果成功會刪除該儲藏。

  ```
  $ git stash branch testchanges
  M	index.html
  M	lib/simplegit.rb
  Switched to a new branch 'testchanges'
  On branch testchanges
  Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)
  
  	modified:   index.html
  
  Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)
  
  	modified:   lib/simplegit.rb
  
  Dropped refs/stash@{0} (29d385a81d163dfd45a452a2ce816487a6b8b014)
  ```

#### 清除工作目錄

對於工作目錄中的一些文件，你想做的可能不是儲藏而是刪除。`git clean` 命令就是用來干這些的。清理工作目錄有一些常見的原因，比如說為了移除由合併或外部工具生成的東西，或是為了運行一個乾淨的建構而移除之前建構的殘留。

**你需要謹慎使用這個命令**，因為它被設計來從工作目錄中移除未被追蹤的文件。如果你改變主意，你也不一定找的回來那些文件的內容。另一個更安全的選項是 `git stash --all` 來移除每一樣東西並存放在棧中。

使用 `git clean -f -d` 命令來移除工作目錄中所有未追蹤的文件以及空的子目錄。`-f`意味著強制或確定要移除，使用它需要 GIT 配置環境變量 `clean.requireForce` 沒有顯式設置為 `false`。

如果只是要看看`git clean`會做什麼，可以加上 `--dry-run` 或 `-n` 選項來運行命令，這意味著做一次演習然後告訴你要移除那些檔案。

默認情況下，`git clean` 命令只會移除沒有忽略的未追蹤文件。任何與 `.gitignore` 或其他忽略文件中的模式匹配的文件都不會被移除。如果你也想要移除那些文件，可以給 `git clean` 命令增加一個 `-x` 選項。

另一個更安全的使用 `git clean` 的方式，可以加上`-i`選項，這將會以交互模式詢問你每個檔案是否要刪除。

> 在一種奇怪的情況下，可能需要格外用力才能讓Git清理你的工作目錄。如果你恰好在工作目錄中復製或克隆了其他Git倉庫（可能是子模塊），那麼即便是 `git clean -fd`都會拒絕刪除這些目錄。這種情況下，你需要加上第二個`-f`選項來強調。

#### 標籤(tag)

GIT 可以給倉庫歷史中的某一個提交打上標籤，以示重要。具體的場景是使用標籤來標記發布節點(v1.0、v2.0等)。

##### 列出標籤

在GIT中列出標籤非常簡單，只需要輸入`git tag`(或帶上可選`-l`、`--list`)

你也可以按照特定的模式查找標籤，如果你只對`v1.8.5`開頭的標籤感興趣的話，可以輸入以下命令

```
git tag -l "v1.8.5*"
```

##### 創建標籤

GIT 支持兩種標籤：「輕量標籤」與「附註標籤」。

輕量標籤很像一個不會移動的分支，它只是某個特定提交的引用。

附註標籤是儲存在 GIT 數據庫中的一個完整對象，它們是可以被校驗的，其中包括打標籤者的名字、電話郵件地址、日期時間，此外還有一個標籤訊息。並請可以使用 GNU Private Gurad(GPG) 簽名並驗證。

###### 附註標籤(annotated)

想要創建附註標籤，最簡單的方式是當你在運行 `tag` 命令時指定 `-a `選項。`-m`選項指定標籤訊息，如果沒有指定，GIT 會開啟編輯器要求你輸入。

```
git tag -a 'v1.0' -m 'version 1.0'
happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/11 (master)
$ git tag
v1.0
```

通過使用 `git show` 命令可以看到標籤訊息和與之對應的提交訊息

```
$ git show v1.0
tag v1.0
Tagger: James <happily1994@gmail.com>
Date:   Tue Mar 30 14:16:35 2021 +0800

version 1.o

commit 5702ae8ab48204523765425d39533687cf3ecbd7 (HEAD -> master, tag: v1.0)
Author: James <happily1994@gmail.com>
Date:   Tue Mar 30 11:57:30 2021 +0800

    4

diff --git a/test.txt b/test.txt
index c47213d..1191247 100644
--- a/test.txt
+++ b/test.txt
@@ -1,3 +1,2 @@
 1
 2
-
```

###### 輕量標籤(lightweight)

輕量標籤本質上是將提交對象哈希值存到一個文件中，沒有保存其他訊息。創建輕量標籤，只需要提供標籤名稱。

```
happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/11 (master)
$ git tag 'v1.0-lw'

happi@LAPTOP-5SML5556 MINGW64 /c/Workspaces/git練習/11 (master)
$ git tag
v1.0
v1.0-lw
```

運行`git show`查看輕量標籤，只會看到對應提交對象訊息。不會有額外的訊息。

```
$ git show 'v1.0-lw'
commit 5702ae8ab48204523765425d39533687cf3ecbd7 (HEAD -> master, tag: v1.0-lw, tag: v1.0)
Author: James <happily1994@gmail.com>
Date:   Tue Mar 30 11:57:30 2021 +0800

    4

diff --git a/test.txt b/test.txt
index c47213d..1191247 100644
--- a/test.txt
+++ b/test.txt
@@ -1,3 +1,2 @@
 1
 2
-
```

###### 提交歷史打標籤

如果忘記為過去的某個重要提交打上標籤，只需要在創建標籤時，指定與之對應的提交對象哈希值。

```
git tag -a 'v0.9' 9fceb02
```

##### 共享標籤

默認情況下，`git push` 命令不會傳送標籤到遠端倉庫服務器上。在創建完標籤後，你必須顯示的推送標籤到服務器上。這個過程就像推送共享遠程分之一樣。

```
git push origin <tagname>
```

> 如果想要一次性推送很多標籤，可以使用帶有`--tags`選項的`git push`命令。這會將所有不再遠端倉庫的標籤都推送上去。

##### 刪除標籤

要刪除本地不需要的標籤，可以使用帶有`-d`選項的`git tag`命令，並在最後指定標籤名。

```
$ git tag -d 'v1.0-lw'
Deleted tag 'v1.0-lw' (was 5702ae8)
```

注意上述命令不會移除遠端倉庫的標籤。

刪除遠端標籤有兩種方式

+ 方式一：推送空值取代遠端目標標籤，達到刪除目的。

  `git push <remote> :refs/tags/<tagname>`

+ 方式二：使用命令刪除

  `git push --delete <tagname>`

##### 檢出標籤

如果想查看標籤所指向的版本，可以使用`git checkout`命令，雖然這會使你處於「分離頭指針(detached HEAD)」狀態，這狀態有一些不好的副作用。在該狀態下，如果你做了某些更改並提交，你的新提交將不屬於任何分之，並且無法訪問，除非通過明確提交對象哈希值才能訪問。因此如果你要修復舊版本的錯誤，通常會創建一個新分之。

```
git checkout -b <new branch name> <tagname>
```

> 注意標籤是不會移動的，如果再檢出該標籤後，又進行了一次提交，此時該分支與標籤所指的提交就有所不同了

#### 遠端倉庫使用

團隊協作會需要遠端倉庫輔助，遠端倉庫可能在私人的伺服器，或是給其他公司託管。你可以有多個遠程倉庫，通常有些倉庫對你只讀，有些則可以讀寫。團隊協作涉及管理遠程倉庫以及根據需要推送或拉取數據。

##### 查看遠程倉庫

如果想看已經配置的遠程倉庫服務器，可以運行`git remote`命令。它會列出每一個遠程倉庫的別名。如果該倉庫是由克隆操作創建，那至少會看到 origin，這是 GIT 為克隆的遠端倉庫創建的默認名字。

如果想查看遠端倉庫別名和與之對應的URL，可以加上`-v`選項`。

```
$ git remote -v
origin  https://github.com/style1994/git-learning (fetch)
origin  https://github.com/style1994/git-learning (push)
```

如果想查看某個遠端倉庫的詳細訊息，使用 `git remote show <remote>` 

```
$ git remote show origin
* remote origin
  Fetch URL: https://github.com/style1994/git-learning
  Push  URL: https://github.com/style1994/git-learning
  HEAD branch: main
  Remote branches:
    main  tracked
    test1 tracked
    test2 tracked
  Local branch configured for 'git pull':
    main merges with remote main
  Local ref configured for 'git push':
    main pushes to main (up to date)
```

##### 添加遠程倉庫

```
git remote add <shortname> <url>
```

##### 拉取遠程倉庫

如想從遠程倉庫獲取數據，通過`git fetch` 命令。它會訪問遠程倉庫，從中抓取你還沒有的數據。執行完後，你會擁有所有那個倉庫所有分支的引用，可以隨時合併和查看。**注意該命令不會自動合併和主動修改你的工作目錄，因為抓取下來的不再本地分支上。`git fetch`命令後會創建與本地分支與遠程分支溝通的媒介「遠程追蹤分支」。**遠端追蹤分支名為 <倉庫別名>/<分支名>，可以隨時切換到該分支查看，確認沒問題後在合併到自己的本地分支中。

```
git fetch <remote>
```

如果本地分支有設置追蹤遠端分支，那麼可以在該分支運行`git pull`命令，來自動抓取遠端倉庫數據並合併遠端分支到當前分支。默認情況下`git clone`會自動設置本地 master 分支追蹤遠端倉庫的 master 分支。

```
git pull <remote>
```

##### 推送到遠端倉庫

如果想分享的項目成果，通過`git push`命令將本地分支推送至遠端倉庫上，團隊的成員也就可以訪問該倉庫獲得分享的數據。

```
git push <remote> <local branch name>
```

> 必須有寫入該遠端倉庫的權限才可以執行 `git push` 命令。如果團隊中有人先推送到遠端，那麼你的推送操作會被拒絕，你必須抓取團隊成員的最新工作成果進行合併，才能執行推送操作。

##### 重命名遠端倉庫

```
git remote rename <current remote name> <new remote name>
```

如果修改的遠端倉庫的名稱，遠端分支的名稱也會隨之變更。

##### 刪除遠端倉庫

```
git remote remove <remote>
```

刪除遠端倉庫後，所有和這個遠端倉庫相關的遠端分支與配置訊息也會一起被刪除。

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