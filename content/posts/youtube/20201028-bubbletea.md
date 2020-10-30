---
title: "YT: Bubble Tea"
date: 2020-10-30T22:17:25+09:00
draft: false
toc: true
tags: ["youtube", "bubbletea", "tui", "go"]
---


2回に分けて放送しました。

## YouTube

#### チュートリアル編

{{<youtube U-LP-7IXZK8>}}

#### 応用編

{{<youtube g_mRfGoX24E>}}

## charmbracelet/bubbletea

[charmbracelet/bubbletea: A powerful little TUI framework 🏗](https://github.com/charmbracelet/bubbletea)

### Why?

今回は、Go言語製のTUIフレームワーク[Bubble Tea](https://github.com/charmbracelet/bubbletea)。

何で知ったかは忘れちゃったけど、READMEに載ってるグラフィカルなTeaminal UIに惹かれました。やっぱり可視化は面白そうに見えちゃうよね。

### What?

Goでインタラクティブかつグラフィカルなコマンドラインアプリケーションを作成できるフレームワーク。
[Bubble Tea](https://github.com/charmbracelet/bubbletea)本体はフレームワークの部分に関するライブラリで、グラフィカルな部分については同じ作者の[Bubbles](https://github.com/charmbracelet/bubbles)の方に別ライブラリとして切り出されている模様。

この作者はGoのコマンドラインに関するライブラリを色々公開していて、Markdownをターミナル上でレンダリングできる[glamor](https://github.com/charmbracelet/glamour)なんかは、GitHub CLI (gh)にも使われている模様。

https://github.com/cli/cli/blob/trunk/go.mod#L9

READMEの先頭にも書いてありますが、Elm言語のアーキテクチャに影響を受けているそう。

> The fun, functional and stateful way to build terminal apps. A Go framework based on The Elm Architecture. 

アーキテクチャと言っても、モデルがあって、モデルの状態によって変化するViewがあって、モデルの状態を変更するUpdateがある、みたいな単純なもの。

### How?

READMEに載ってるTODOアプリのチュートリアルが、サクッと試せるので、まずは写経して動かしてみるのがお勧め。

前述したように、Bubble TeaはModel、View、Updateを使ってターミナルアプリケーションを作っていきます。

具体的には、[`tea.Model`](https://pkg.go.dev/github.com/charmbracelet/bubbletea#Model)のインタフェースを実装した構造体を作ります。

```go
type Model interface {
	// Init is the first function that will be called. It returns an optional
	// initial command. To not perform an initial command return nil.
	Init() Cmd

	// Update is called when a message is received. Use it to inspect messages
	// and, in response, update the model and/or send a command.
	Update(Msg) (Model, Cmd)

	// View renders the program's UI, which is just a string. The view is
	// rendered after every Update.
	View() string
}
```

`Model`を実装する構造体の持つフィールドは自由に定義できて、最終的に`View`メソッドの戻り値`string`が、ターミナルに表示される文字列になります。[examples](https://github.com/charmbracelet/bubbletea/tree/master/examples)をみると、`View`メソッドの中で[bubbles](https://github.com/charmbracelet/bubbles)のグラフィカルなコンポーネントを使ったり、[termenv](https://github.com/muesli/termenv)でスタイル指定したりしてますね。（一部のbubblesコンポーネントは`Update`なんかにも絡んで来ますが）

`Update`でモデルの状態を変化させて、Viewの表示を変えるという動きですが、`Update`が実行されるイベントは色々あります。`Update`の引数[Msg](https://pkg.go.dev/github.com/charmbracelet/bubbletea#Msg)に、`Update`をトリガするイベントの内容が入っていますが、キー入力の[`KeyMsg`](https://pkg.go.dev/github.com/charmbracelet/bubbletea#KeyMsg)だったり、マウス移動の[MouseEvent](https://pkg.go.dev/github.com/charmbracelet/bubbletea#MouseEvent)だったり、[Tick](https://pkg.go.dev/github.com/charmbracelet/bubbletea#Tick)で定期的にイベントを発火するものだったり。（この辺の使い方は[examples](https://github.com/charmbracelet/bubbletea/tree/master/examples)を参照）


### Impression
`Model`、`Update`、`View`の関係は頭に入って来やすいし、チュートリアルのTODOアプリなんかもサクっと作れたりするので便利そう。ただ、Bubbleコンポーネント使ってグラフィカルだったり、動きの多いものを作ろうとすると、色々ややこしくなってくるので慣れないと辛そう。

更新頻度はまだまだ高いので、もう少し落ち着くところまで行ったら使いたいですね〜（用途思いついてないけど）

## Contribution
特になし（そろそろ何かしたい...）