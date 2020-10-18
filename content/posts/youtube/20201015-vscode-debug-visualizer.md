---
title: "YT: debug-visualizer"
date: 2020-10-15
draft: false
---

ようやく第０回目のテスト配信ができた...。

元々、10月に開始されるHacktoberfestに乗じてOSS Contributionの模様を配信できればなーなどと考えてたけど、開始早々にスパムPRが話題[^1]になり乗っかりづらい感じになったり、方針転換してOSS触ってみた配信（ついでにContrib）をやろうと思ったらYouTubeの配信申請が一週間ぐらい放置されたり...。

苦節二週間ぐらい。

## [hediet/vscode-debug-visualizer](https://hediet/vscode-debug-visualizer)

https://www.youtube.com/watch?v=Dtqf2IlqDsY&feature=youtu.be

第０回目はVSCodeのデバッグ時にデータ構造を視覚的に表現できるExtension。

HackerNewsのツイートか何かで流れてきて、内容もさることながら、作者が[VSCode Draw.io extension](https://marketplace.visualstudio.com/items?itemName=hediet.vscode-drawio)の作者ということで気になってました。

### インストール
インストールはVS Codeの画面から、"Debug Visualizer" で検索してインストールボタンを押すだけ。

### 使用方法
ドキュメントがあまり書かれてなくて、VS Codeの`cmd + shift + p`から "Debug Visualizer: New View" を入力して出てくるDebug Visualizer用のタブを出した後から何をすればいいのかよく分からなかった。

どうやら、Visualize対象にできるのは特定の形式のJSON Stringだけらしく「デバッグ中にこの変数みたいな」ぐらいの手軽さでは使えない模様。(JS系だと敷居は下がるのかも)

公式に[Goのデモ用のコード](https://github.com/hediet/vscode-debug-visualizer/blob/master/demos/golang/demo.go)があったので、それをコピペして、テストをデバッグ実行かけて、適当なところにブレークポイントおいて止めて、Debug Visualizer用のタブで `visualize()` というコマンドを実行するとようやくvisualizeできた。道のりは長かったけど、やっぱり視覚的にヌルヌル動くのは面白いですね。

### Contribution
特になし


[^1]: [DigitalOcean's Hacktoberfest is Hurting Open Source](https://blog.domenic.me/hacktoberfest/)