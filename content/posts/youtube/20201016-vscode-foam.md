---
title: "YT: Vscode Foam"
date: 2020-10-16T11:58:21+09:00
img: 20201016/yt.png
draft: false
toc: true
tags: ["youtube", "vscode", "foam"]
---

## YouTube
{{< youtube MKeA6N0Bz9o>}}

## foambubble/foam
[foambubble/foam: A personal knowledge management and sharing system for VSCode](https://github.com/foambubble/foam)

### Why?
第1回目はVSCode上でPersonal Knowledge ManagementができるというFoam。
あまり覚えてないですが、Twitter見てる時に流れてきて、GitHubリポジトリのReadmeに貼ってあった画像が面白そうだったので興味を持ったもの。

エディタ上でKnowledge管理とは？という点が気になったので触ってみたかった。

まず、[Foam for VSCode](https://marketplace.visualstudio.com/items?itemName=foam.foam-vscode)のページにもありますが、Foam単体では動作できないため、Foam用のVS Code Extensionだけでなく、推奨のExtensionもインストールしなきゃ動かない、と。あと、`extremely early stage software`と言っているので、まだ実用段階ではなさそうですね。

### What?
[公式ドキュメント](https://foambubble.github.io/foam)によると、[Foam](https://foambubble.github.io/foam/#getting-started)は[Roam Research](https://roamresearch.com)というサービスにインスパイアされたもので、[Roam Research]((https://roamresearch.com))と同様のKnowledge管理をVS CodeとGitHubで表現しようというものらしい。

Markdown?形式の記述を元にしていて、編集をVS Codeで、データの管理をGitHubでやる模様。編集したものはGitHubで管理されるため、GitHub PagesやNetlify, Verselなんかを使えば簡単にPublishできるようにしている模様。

### How?
[公式ドキュメントの手順](https://foambubble.github.io/foam/#getting-started)に従うと、まずはGitHub上の[Foam用のテンプレートリポジトリ](https://github.com/foambubble/foam-template)[^1]から派生したリポジトリを作るらしい。

[動画(6:01~)](https://youtu.be/MKeA6N0Bz9o?t=361)

https://github.com/foambubble/foam-template の "Use this template" ボタンから自分用のリポジトリを作り、できたリポジトリをcloneしてVS Codeで開け、と。

VS Codeで開くと、推奨のExtensionをインストールするよう右下に指示が出るため、中身を確認しながらインストール。インストールされるのは下記のExtension。
* [Foam for VSCode (Wikilinks to Markdown) - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=foam.foam-vscode)
* [Prettier - Code formatter - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
* [GitLens — Git supercharged - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)
* [Markdown All in One - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one)
* [Markdown Notes - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=kortina.vscode-markdown-notes)
* [Markdown Links - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=tchayen.markdown-links)
* [Gray Matter - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=philipbe.theme-gray-matter)

![install recommendations](/images/20201016/install-recommendations.png)

結構多いですね。そして、リポジトリの`.vscode/extensons.json`に推奨のExtensionのIDを書いておくと、リポジトリのコードをVS Codeで開いた時にインストール指示を出すことができるんですね。初めて知った。

[foam-template/extensions.json at master · foambubble/foam-template](https://github.com/foambubble/foam-template/blob/master/.vscode/extensions.json)

上記のExtensionのインストールが完了した後、`Cmd + Shift + p`でVS Codeのコマンドパレットを開き、"Show Graph" と入力すると出てくるメニューを実行。すると、Markdown Linksのタブが開きます。

![foam](/images/20201016/foam-first.png)  
[動画(15:47~)](https://youtu.be/MKeA6N0Bz9o?t=947)

どうやら、開いているリポジトリ内にあるMarkdownから、別ページを参照している記述を見つけ、その関係をグラフ表示してくれるらしい。リンク文字列 `[[page]]` を追加して保存すると、リアルタイムでノードを増やしてくれる。(`foam-tips`へのリンクは反応してくれませんでしたが...)

他にもいろいろみてみましたが、現状はこのページ間の関係性を表すグラフを表示してくれるのがメインの機能な感じでした。GitHub Pagesに公開する用っぽい layout ファイルと style ファイルもありましたが、あまり驚きそうな期待もなかったのでそこまでは試しませんでした。

最後に、ファイル数が多くなったときの表示を確認するため、ファイル数が多そうというイメージで [MicrosoftDocs/azure-docs](https://github.com/MicrosoftDocs/azure-docs)を表示してみました。

結果は...  

![azure-docs on foam](/images/20201016/try-azure-docs.png)  

ファイル数が100を超えたりすると、ちょっと見るのは辛そうですね。数十ファイルレベルまでが限界かと思いました。

### Impression
* まだまだアーリーステージで実用的ではなさそう
* グラフ表示も数十ファイルまで限界という感じで、そのレベルだとKnowledge Managementとしては少し弱い
* [Markdown Links](https://marketplace.visualstudio.com/items?itemName=tchayen.markdown-links)を中心とした VS Code Extension と GitHub の組み合わせで Value を出そうという感じだけど、[Markdown Links](https://marketplace.visualstudio.com/items?itemName=tchayen.markdown-links) だけで十分かなという印象
  * 既存機能の組み合わせでも [fastai/fastpages](https://github.com/fastai/fastpages) なんかはグッとくるものがあった

## Contribution

特になし

[^1]: [Creating a template repository - GitHub Docs](https://docs.github.com/ja/free-pro-team@latest/github/creating-cloning-and-archiving-repositories/creating-a-template-repository)