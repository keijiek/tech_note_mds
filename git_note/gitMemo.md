# Git Commands

---
## WorkTree で使う

### status

- git **`status`**
    - WorkTree傘下の全ファイル(無視対象を除く)の、登録状態を確認

### checkout

- git **`checkout --`** *filePath*
    - Work Treeにある対象ファイルを、直前にコミットした状態(Git directoryの状態)に戻す。
    - ファイル新規作成時やファイル名変更時の後など、思った通りの挙動にならない状況もある。
    - **`--`** を忘れないように。

### init

- git **`init`**
    - WorkTreeにしたいディレクトリで実行し、そこをGitの管理下に置く。

### rm

- git **`rm`** *file/dirPath*
    - 対象をWorkTreeから削除し、かつ、削除した状態をStagingAreaに登録する。
    - Git管理下のファイル等は、この方法で削除するのが望ましい。
        - git rmに頼らず削除した場合、例えば素のrmコマンドやExplorer上で削除した場合、自分でaddしてその状態を登録する必要がある。

- git **`rm -r`** dirPath
    - 中身を持つディレクトリを削除する場合、-r optionを書く。recursive.

---
## **Add** : WrokTree から StagingArea へ

### add

- git **`add`** *file/Directory'sPath*
    - 対象を Staging Area に登録
    - [git add **`.`**] と命令すれば、カレントディレクトリが対象になる

- git **`add -A`** / git **`add -all`**
    - 変更したファイルをすべて Staging Area に登録

### reset

- git **`reset HEAD`** *file/dirPath*
    - 直前(HEAD)のaddを無かったことにする。
    - WorkTreeへの影響はない。

### diff

- git **`diff`** ; add した後の Work Tree でどんな変更が行われたかを詳しく確認(テキスト閲覧)
- git **`diff --cached`** ; Staging Area にどんな変更が登録されているか

---
## **Commit** : StagingArea から GitDirectory へ

### commit

- git **`commit`**
    - Staging Areaの登録状態を、Git directoryへコミットする。
        1. VScodeが自動起動する (core.edhitor = **"code --wait"** の場合)
        1. コメントを書く
            - 1行目にタイトル、2行目は空行、3行目に詳細、というコメントが望ましい。
        1. 保存(ctrl+s)=>閉じる(ctrl+w)、でコミット開始

- git `commit` **`-m`** **`"hogeHoge"`**
    - "hogeHoge"という1行コメントで、すぐにコミット。エディタ起動なし。

- git `commit` **`-a`**
    - まず、更新されたファイルをStagingAreaに反映し、その後にcommitを行う。
    - ただし、まだGit管理下にないファイルの更新はこの方法では反映されないので、手動で add 命令を出す必要がある。

- git `commit -am "hogeHoge"` ;   -a と -m を両方おこなう。

### log

- git **`log`**
    - GitDirectoryに対するコミットの履歴を見る

---
## Branch (ブランチ) の操作

### branch

- git **`branch`**
    - いま存在する全てのbranchを表示。「*」接頭文字はいま使用中のbranchであることを示す

- git **`branch`** *branchName*
    - その名称のbranchを作成

### branch 削除
- git **`branch --delete`** *branchName* / git **`branch -d`** *branchName*
    - 対象branchがmerge済みなら削除
    - option は、 **`--delete`** か **`-d`** のどちらでもよい

- git **`branch -D`** *branchName*
    - merge済みか否かに関わらず削除

- remoteRepository 上では git push を使用(後述)

### checkout

- git **`checkout`** *targetBranc'shName*
    - 対象ブランチに操作を切り替え、同時に、そのブランチの内容をワークツリーに反映させる？。編集済みのワークツリーが上書きされてしまうときはエラーがでるらしい。
    - ブランチを切り替えた後のコミット等の操作は、そのブランチに対して行われる。
    - 例 : git checkout master

- git **`checkout -b`** *newBranch'sName*
    - 新規ブランチ作成、かつ、操作ブランチをそれに切り替える

### diff

- git **`diff`** *comparisonBranch*
    - 比較対象branchと、操作中branchとの差分を表示する。

### merge

- git **`merge`** *mergingBranche*
    - mergeする側のbranch(merge元)の内容を、操作中branchに対してmergeする
        - エディタが自動起動。マージコメントの編集は不要。すぐ閉じてよい。
    - 事前に、mergeを受け入れる側のブランチへ操作を切り替えておく(checkoutしておく)必要がある。

---
## Git Config

- git `config --list` => 設定一覧の確認(item名と値の組み合わせで一覧表示)
- git `config` *item'sName* => そのitemの値を見る
- git config **`--global`** *Item* *Value* =>項目の値を登録/変更
    - git config **`--global user.name`** *userName* => ユーザ名の登録/更新
    - git config **`--global user.email`** *mailAddress* => メールアドレス登録/更新
    - git config **`--global core.editor`** "code --wait" => git命令中に自動起動するエディタの起動命令文

---
## .gitconfig の path    (~/.gitconfig)

- そのユーザーの home directory(`~`) の下に `.gitconfig` という設定ファイルが存在する。
- 内容の登録/更新は上記の `git config --global` で行う。
- `less ~/.gitconfig` で閲覧できる。

---
# GitHubを絡めたGitコマンド

## GitHub上で他のリポジトリをフォークする

- 対象リポジトリのページで、右上の"`Fork`"を押す。アカウント選択をせまられたら自分のを選択。
- 自分のアカウントに、上記のフォークが、リモートリポジトリとして作成される。

---
## GitHub.com上の自分のリモート・リポジトリを、ローカル・リポジトリとして取得

- git **`clone`** *address*
    1. addressの取得(ブラウザ):
        1. ローカル・リポジトリにしたいリモート・リポジトリのページをブラウザで開く
        1. リモート・リポジトリのページの、緑ボタン[Clone or download]を押す
        1. [Clone with SSH]の状態であることを確認。そうでなければ変更。
        1. 表示URLの横にあるコピーアイコンを押すと、URLがクリップボードに保存される
    1. bashでgit clone と書き、クリップボードのURLをペースト
        - **[Shift]+[Insert]** ： GitBash上にペーストするショートカット
    1. 実行すると、カレント(.)にローカル・リポジトリのディレクトリが作成される。

---
## **Push** : LocalRepository から RemoteRepository へ
- push コマンドの操作対象は、あくまで branch のようだ。

### push

- git **`push`** *remoteRepository* *branch*
    - remoteRepositoryのbranchに対し、localRepositoryから該当branchをプッシュ。
    - Remote-Repository内にそのbranchが無いなら、同名のbranchが作られる。
    - 1つのRepositoryには、同名のbranchは1つしか存在できない。

### push --delete

- git **`push --delete`** *remoteRepositoryName* *branchName*
    - remoteRepository 上の不要ブランチを削除
    - 例: git push --delete origin sessions-info
        - "origin" という remoteRepository の "sessions-info" という branch を削除。
        - git `push` origin `:`sessions-info と書いても同じ命令になる。branch名の先頭に":"が付いている

- なお、GitHub上における次の操作のいずれかでも消すこともできる。
    - merge 後の "Delete branch" ボタンをクリック。 または、
    - [Code]タブ => [*n* branches]と辿り、branch 一覧のゴミ箱アイコンをクリックする

---
## Pull / Fetch

- git **`pull`** *remoteRepository'sName* *branch'sName*
    - そのremoteRepositoryに属するそのbranchを、
        1. ローカルリポジトリに反映し、かつ
        1. ワークツリーにも反映する。
    - 例：git `pull` origin master
        - "origin"は対象となるリモートリポジトリ名で、"master"はそれに属する対象ブランチ名
    - ローカルに、対象ブランチに対応するブランチが存在しなければならない。

- git **`fetch`** *remoteRepository'sName*
    - そのremoteRepositoryの内容を、localRepositoryに反映する。
    - 例：git `fetch` origin
        - リモート・リポジトリ名originをの内容を、ローカルリポジトリに反映する
    - その後、[git checkout *branch'sName*]と命令すれば、そのブランチをローカルのワークツリーに反映できる。
        - その際、対応するブランチが存在しなければ自動で新規作成する

---
## VScode の起動

- **`code`** *filePath*
    - VScodeが起動し、対象ファイルを開く。
    - 対象ファイルが無いなら、その名前を持つ新規ファイルの編集が始まる。
        - それを上書き保存すれば、その名前のファイルが作られる

- **`code`** *dirPath*
    - VScodeが起動し、対象ディレクトリが左側に展開されるので、そこで作業。
    

---
## ファイルをwindows既定のプログラムで開く

- **`start`** *filePath*
    - 例: start index.html  (そのhtmlを既定ブラウザで開く)