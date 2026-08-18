# CW-arc

Codex用のローカルPlugin配布リポジトリです。

## 含まれるPlugin

- `chatwork-archive-completion`

Chatworkアーカイブログから社内ナレッジ用Markdownレポートを作成し、指定のGoogle Driveフォルダへ格納したうえで、CWアーカイブ管理スプレッドシートの該当行を`TRUE`に更新するためのスキルです。

## 使う側のインストール手順

### コマンドを実行する画面の開き方

Mac、Windows、Linuxのどれでも利用できます。自分の環境に合わせてコマンドを実行する画面を開いてください。

Macの場合:

1. `command + space`を押してSpotlight検索を開く
2. `ターミナル` と入力する
3. `Terminal` または `ターミナル` を選んでEnterを押す

Windowsの場合:

1. スタートメニューを開く
2. `PowerShell` と入力する
3. `Windows PowerShell` または `PowerShell` を開く

Linuxの場合:

1. アプリ一覧から `Terminal` を開く
2. Ubuntuなら `Ctrl + Alt + T` でも開けます

画面が開いたら、下の3行をまとめてコピーして貼り付け、Enterを押します。

Mac / Linux:

```bash
git clone https://github.com/takeuchi23-jpg/CW-arc.git ~/CW-arc
codex plugin marketplace add ~/CW-arc
codex plugin add chatwork-archive-completion@hiroto
```

Windows PowerShell:

```powershell
git clone https://github.com/takeuchi23-jpg/CW-arc.git $HOME\CW-arc
codex plugin marketplace add $HOME\CW-arc
codex plugin add chatwork-archive-completion@hiroto
```

1行ずつ実行したい場合は、次の手順で進めてください。

このリポジトリをcloneします。

Mac / Linux:

```bash
git clone https://github.com/takeuchi23-jpg/CW-arc.git ~/CW-arc
```

Windows PowerShell:

```powershell
git clone https://github.com/takeuchi23-jpg/CW-arc.git $HOME\CW-arc
```

CodexにこのPluginマーケットプレイスを登録します。

Mac / Linux:

```bash
codex plugin marketplace add ~/CW-arc
```

Windows PowerShell:

```powershell
codex plugin marketplace add $HOME\CW-arc
```

Pluginを追加します。

```sh
codex plugin add chatwork-archive-completion@hiroto
```

Codexを再起動するか、新しいタスクを開始すると、次のスキルを使えるようになります。

```text
$chatwork-archive-completion
```

## 更新を受け取る方法

配布元がGitHubに変更をpushしたあと、使う側はclone済みのフォルダでpullします。

Mac / Linux:

```bash
cd ~/CW-arc
git pull
codex plugin add chatwork-archive-completion@hiroto
```

Windows PowerShell:

```powershell
cd $HOME\CW-arc
git pull
codex plugin add chatwork-archive-completion@hiroto
```

更新後はCodexを再起動するか、新しいタスクを開始してください。

## 自分用に派生させる方法

使う側はこのリポジトリをforkするか、cloneしたフォルダをコピーして、自分用に編集できます。

派生版を作る場合は、少なくとも次を変更してください。

- `plugins/chatwork-archive-completion/.codex-plugin/plugin.json` の `name`
- `plugins/chatwork-archive-completion/skills/chatwork-archive-completion/SKILL.md` の `name`
- `marketplace.json` 内のPlugin `name`

同じ`name`のまま複数の派生版を入れると、Codex側で見分けづらくなります。

## 必要な権限

このPluginはChatwork、Google Drive、Google Sheetsを扱います。利用者側でも対象サービスへのログインと、対象フォルダ・スプレッドシートへのアクセス権が必要です。
