# ターミナルのカスタマイズ

## ターミナルのカスタマイズ方法
~/.bashrc, ~/.zshrcを編集する。

変更を反映するには下記コマンドを実行する。
```terminal
$ source ~/.bashrc
```
また、起動時に毎回読み込むようにするには.bash_profileを作成し、下記を追加する。
```~/.bash_profile
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi
```

## プロンプトの設定方法
プロンプトを編集するには以下のように`export PS1=''`で設定する。
```~/.bashrc
export PS1='Hello Prompt! \# \t \w \$ '
```

## プロンプト（入力部分の左側）設定用記号一覧

|記号| 意味　　　　　　　　　　　| 例                                        |
|\h | ホスト名	　　　　　　　　| hamajunnoMacBook-Pro　                    |
|\u | ユーザ名	　　　　　　　　| hamajun                   　              |
|\w | ディレクトリ（フルパス）	| ~/Documents/GitHub                      　|
|\W | ディレクトリ	          | GitHub                                 　 |
|\t | 時間 (24形式)  　　　　　| 23:29:38                               　 |
|\T | 時間 (12形式) 　　　　　 | 11:29:38                                　|
|\@ | AM / PM　　　　　　　　　| PM　　　　　　　　　　　　　　　　　　　　　　   |
|\d | 日付	　　　　　　　　　　| Wed Jun 20　　　　　　　　　　　　　　　　　　　|
|\D | 日時　　　　　　　　　　  | \D{%y/%m/%d %H:%M:%S} 18/06/20 23:29:38 　|
|\# | コマンドの番号　　　　　  | (1, 2, 3...)	1                        　 |
|\n | 改行	　　　　　　　　　　|　　　　　　　　　　　　　　　　　　　　　　　　　 ｜
|\$ | 一般ユーザーの時 $、rootの時 # を表示	$ | $                         　 |


## 書体、文字色の変更
これらを変更する場合は、エスケープシーケンスを使用する必要がある。
「エスケープシーケンス」とは、文字出力の制御を行う特殊な文字列。
`\[` と `\]`で囲んで表現する。

### 書体

|値|効果|
|0m|装飾なし|
|1m|太字|
|2m|細字（未対応？）|
|3m|イタリック体|
|4m|下線|

例
```~/.bashrc
export PS1='装飾なし \[\e[1m\] ここは太字 \[\e[0m\] 装飾なし \$ '
```
`\e[` はASCIIのエスケープ文字を使うよという意味

### 文字色、背景色

|値|文字色|値|背景色|
|30m|黒|40m|背景黒|
|31m|赤|41m|背景赤|
|32m|緑|42m|背景緑|
|33m|黄|43m|背景黄|
|34m|青|44m|背景青|
|35m|紫|45m|背景紫|
|36m|水|46m|背景水|
|37m|灰|47m|背景灰|
|39m|デフォ|49m|背景デフォ|

例
```~/.bashrc
export PS1='装飾なし \[\e[32m\] ここは緑色 \[\e[39m\] ここからはデフォ色 \$ '
```

## アウトプット後に改行を入れる

```~/.bashrc
# 出力の後に改行を入れる
function add_line {
  if [[ -z "${PS1_NEWLINE_LOGIN}" ]]; then
    PS1_NEWLINE_LOGIN=true
  else
    printf '\n'
  fi
}
PROMPT_COMMAND='add_line'
```
「シェルスクリプト」を使用する。
`PROMPT_COMMAND` はプロンプトが表示される度に呼び出されるBashの特殊変数。

## Gitブランチを表示する（左寄せ）

https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh
を.git-prompt.shというファイル名でホームフォルダ(~)に保存する。([Raw]→右クリック保存)

```~/.bashrc
# show git branch
# https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh
source ~/.git-prompt.sh

export PS1='\h:\W \u \[\e[1;32m $(__git_ps1 "(%s)") \[\e[0m\] \$ '
```

.git-prompt.shは、Git公式が用意したシェルスクリプトで、これを読み込む。
$()はコマンドを呼び出すための記号。__git_ps1は関数で、引数の"(%s)"はフォーマット指定子です。
(%s)の場合、(master)、<< %s >>の場合、<< master >>となります。

## Gitブランチを表示する（右寄せ）
上記だとプロンプトの横、つまり左寄せで表示されるので、右側表示したい場合は以下をコピペ。
`export PS1`の部分は適宜カスタマイズ

```~/.bashrc
function length()
{
  echo -n ${#1}
}

function init-prompt-git-branch()
{
  git symbolic-ref HEAD 2>/dev/null >/dev/null &&
  echo "($(git symbolic-ref HEAD 2>/dev/null | sed 's/^refs\/heads\///'))"
}

if which git 2>/dev/null >/dev/null
then
  export PS1_GIT_BRANCH='\[\e[$[COLUMNS]D\]\[\e[1;31m\]\[\e[$[COLUMNS-$(length $(init-prompt-git-branch))]C\]$(init-prompt-git-branch)\[\e[$[COLUMNS]D\]\[\e[0m\]'
else
  export PS1_GIT_BRANCH=
fi
export PS1="\[\e[32;1m\]\u@\H \[\e[33;1m\]\w $PS1_GIT_BRANCH\n\[\e[36;1m\]\t \[\e[0m\]\$ "
```

## 僕の考えた最強のターミナル画面（随時更新）
フォントは `Andale Mono 12 pt.`
```~/.bashrc
# 出力の後に改行を入れる
function add_line {
  if [[ -z "${PS1_NEWLINE_LOGIN}" ]]; then
    PS1_NEWLINE_LOGIN=true
  else
    printf '\n'
  fi
}
PROMPT_COMMAND='add_line'

# Gitブランチを右側表示
function length()
{
  echo -n ${#1}
}

function init-prompt-git-branch()
{
  git symbolic-ref HEAD 2>/dev/null >/dev/null &&
  echo "($(git symbolic-ref HEAD 2>/dev/null | sed 's/^refs\/heads\///'))"
}

if which git 2>/dev/null >/dev/null
then
  export PS1_GIT_BRANCH='\[\e[$[COLUMNS]D\]\[\e[1;31m\]\[\e[$[COLUMNS-$(length $(init-prompt-git-branch))]C\]$(init-prompt-git-branch)\[\e[$[COLUMNS]D\]\[\e[0m\]'
else
  export PS1_GIT_BRANCH=
fi

# 出力
export PS1="\[\e[32m\][\u@\h: \W] $PS1_GIT_BRANCH\n\[\e[36;1m\]\t \[\e[0m\]\$ "
```

## 参考
- ターミナルの表示内容(プロンプト)の変更・カスタマイズ方法まとめ - Qiita[https://qiita.com/hmmrjn/items/60d2a64c9e5bf7c0fe60]
- bashのプロンプトにブランチ名を右寄せ表示する - ぼくのぬまち 出張版[https://notogawa.hatenablog.com/entry/20120720/1342756620]
- Mac OS Xではターミナル起動時に.bashrcが自動で読み込まれない[https://geek-memo.com/bashrc/]
- Macターミナルのデフォルト背景色を変更する - Qiita[https://qiita.com/yuuichi-s/items/f2332bd4b6194bbb5187]
- 最高に見やすいターミナルのフォントを選ぶ – joppot[https://joppot.info/2013/12/12/200#:~:text=%E3%82%BF%E3%83%BC%E3%83%9F%E3%83%8A%E3%83%AB%E3%82%92%E8%B5%B7%E5%8B%95%E3%81%97%E3%81%BE%E3%81%99,%E3%83%95%E3%82%A9%E3%83%B3%E3%83%88%E3%82%A6%E3%82%A3%E3%83%B3%E3%83%89%E3%81%8C%E7%8F%BE%E3%82%8C%E3%81%BE%E3%81%99%E3%80%82]



