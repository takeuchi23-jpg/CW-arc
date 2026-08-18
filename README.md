# CW-arc# Hiroto Codex Plugins

Codex用のローカルPlugin配布リポジトリです。

## 含まれるPlugin

- `chatwork-archive-completion`

Chatworkアーカイブログから社内ナレッジ用Markdownレポートを作成し、指定のGoogle Driveフォルダへ格納したうえで、CWアーカイブ管理スプレッドシートの該当行を`TRUE`に更新するためのスキルです。

## 使う側のインストール手順

このリポジトリをcloneします。

```bash
git clone https://github.com/YOUR_ACCOUNT/codex-plugins-chatwork-archive-completion.git ~/codex-plugins-chatwork-archive-completion
```

CodexにこのPluginマーケットプレイスを登録します。

```bash
codex plugin marketplace add ~/codex-plugins-chatwork-archive-completion
```

Pluginを追加します。

```bash
codex plugin add chatwork-archive-completion@hiroto
```

Codexを再起動するか、新しいタスクを開始すると、次のスキルを使えるようになります。

```text
$chatwork-archive-completion
```

## 更新を受け取る方法

配布元がGitHubに変更をpushしたあと、使う側はclone済みのフォルダでpullします。

```bash
cd ~/codex-plugins-chatwork-archive-completion
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
