---
title: "YT: Actionsflow"
date: 2020-10-24T10:29:30+09:00
draft: false
toc: true
tags: ["youtube", "vscode", "foam"]
---


## YouTube

{{<youtube nZiP10uqdvE>}}

## actionsflow/actionsflow

[actionsflow/actionsflow: The free Zapier/IFTTT alternative for developers to automate your workflows based on Github actions](https://github.com/actionsflow/actionsflow)

### Why?

今回は、GitHub Actionsを使って[IFTTT](https://ifttt.com)/[Zapier](https://zapier.com)のようなワークフロー実行基盤を構築できるという [Actionsflow](https://actionsflow.github.io/) 。

これを知ったきっかけは [StackShare](https://stackshare.io/) からのメール。[StackShare](https://stackshare.io/) のメールを購読しておくと毎週新しめのOSS紹介メールを送ってくれるので結構好き。「GitHub Actions で IFTTT みたいなの組めないか」という話はいくつか見た事があって、まさにドンピシャなものが出てきたので、どこまで使えるものなのかなと。

### What?

IFTTT/Zapierのようなサービス間を繋ぐワークフローの実行基盤としてGitHub Actionsを使おうというもの。GitHub Actionsを使っているので、無料で利用できる。（GitHub Actionsにも[実行上限]([StackShare](https://stackshare.io/)があるので、無限に無料というわけではないけど)

https://actionsflow.github.io/docs/#features

上記の公式サイトの説明を見ると、ワークフロー実行トリガーはコミュニティによって提供されたものが利用可能、ワークフロー内のアクションはGitHub Actionsのものがそのまま使えるらしい。

#### 独自ワークフローの実行には act を利用

> Actionsflow uses act (a tool for running GitHub Actions locally) to run the jobs on your workflow file. 

Actionsflowはワークフローの実行に [act](https://github.com/nektos/act) を使っているらしい。[act](https://github.com/nektos/act) はGitHub Actionsをローカルで実行できるツールで、これをGitHub Actions 上で動かしているので、Actionsflow は **Actions(act) on Actions(GitHub Actions)** のような形で動いているらしい。なるほど。

![actions-on-actions](/images/20201023/actions-on-actions.drawio.png)

#### Actionsは5分ごとに実行

> The workflow can check and run every 5 minutes based on Github actions scheduled events.

**Actions(act) on Actions(GitHub Actions)** の **Actions(GitHub Actions)** が5分ごとに実行され、**Actions(act)** 関連リソースに更新があった場合のみ **Actions(act)** の方のワークフローが実行される感じらしい。例えば、RSSフィードを取得する **Actions(act)** を組んでいた場合、新たなフィードがあった場合のみワークフローが実行されるという感じ。（GitHub ActionsのCacheを使って判断してるのかな？）

#### Webhookによるトリガ

基本的には上述のように5分ごとに更新をチェックする感じで動作が開始されるが、GitHub の Webhook にリクエストを送る事で動作を開始するよう設定することもできるらしい。（今回は使わなかったけど）

https://actionsflow.github.io/docs/concepts/#repository_dispatch-run

GitHub Actions機能である [`repository_dsipatch`](https://docs.github.com/en/free-pro-team@latest/actions/reference/events-that-trigger-workflows#repository_dispatch) イベントを使ってるらしい。Actionsflowでは、このWebhookへのリクエストを送信しやすくするツールなんかも公開している。
[actionsflow/webhook2github: Forward webhook to github repository_dispatch event](https://github.com/actionsflow/webhook2github)


あと、データをフィルタリングするためにMongoDBのクエリを使えるという記述もあるけど、これはよくわかりませんでした。

### How?

実行手順。
[Actionsflow Introduction | Actionsflow Documentation](https://actionsflow.github.io/docs/#quick-start)

Actionsflow もテンプレートリポジトリからリポジトリを作成して使い始めるやり方を推奨している模様。テンプレートリポジトリは色んなところで採用されてますね。

[actionsflow/actionsflow-workflow-default: Actionsflow workflow template repository. The best Zapier/IFTTT free alternative for developers to automate your workflows based on Github actions](https://github.com/actionsflow/actionsflow-workflow-default)

実行手順のドキュメントにあるリンクをクリックすると、GitHubにログインしている状態ならそのままリポジトリ作成画面に遷移するので、リポジトリ名を入力するだけ。

テンプレートリポジトリからリポジトリを作成すると、すでにGitHub Actions実行のためのファイルが置いてあるので、そのままActionsの実行が始まってしまいます。本当にリポジトリを作成するだけ。

### Actions (GitHub Actions)

**Actions(act) on Actions(GitHub Actions)** の **Actions(GitHub Actions)** の方は、下記のような定義。

```yaml:.github/workflows/actionsflow.yml
name: Actionsflow
on:
  repository_dispatch:
  workflow_dispatch:
  schedule:
    - cron: "*/5 * * * *"      ... [1]
  push:
jobs:
  run:
    runs-on: ubuntu-latest
    name: Run
    steps:
      - uses: actions/checkout@v2
      - name: Run Actionsflow
        uses: actionsflow/actionsflow-action@v1
        with:
          args: build
          json-secrets: ${{ toJSON(secrets) }}
          json-github: ${{ toJSON(github) }}
      - name: Setup act                                    ... [2]
        uses: actionsflow/setup-act-for-actionsflow@v1
      - name: Run act
        run: act --workflows ./dist/workflows --secret-file ./dist/.secrets --eventpath ./dist/event.json --env-file ./dist/.env -P ubuntu-latest=actionsflow/act-environment:v1 -P ubuntu-18.04=actionsflow/act-environment:v1
```

`on.schedule.cron`[1] の方で、5分ごとに実行されるよう設定されている。`on.repository_dispatch`や`on.workflow_dispatch`で、WebhookでやWorkflowからの実行を定義しているんだろうなぁ。
`Setup act`[2] や `Run act` のあたりで、**Actions(act)** の部分にあたる [act](https://github.com/nektos/act) のセットアップや実行を行っている。本当にActionsの上でActionsを動かしてるんだなぁ...。`act` は、`workflows/`ディレクトリ内のワークフローファイルを実行しているらしい。

リポジトリの `workflows/` ディレクトリ配下をみてみると、`rss.yml` が置いてあるので、これが `act` によって実行されている模様。

![actions-on-actions-files](/images/20201023/actions-on-actions-files.drawio.png)


#### Actions (act)

`act` によって実行される Actions の定義。
（[ドキュメントに書いてある例](https://actionsflow.github.io/docs/#quick-start)とは内容が違うけど、動きが早くて直してる暇ないんだろうな）

```yaml:rss.yml
on:
  rss:
    url: https://hnrss.org/newest?points=300&count=3     ... [3]
jobs:
  print:
    name: Print
    runs-on: ubuntu-latest
    steps:
      - name: Print Outputs
        env:
          title: ${{on.rss.outputs.title}}
          contentSnippet: ${{on.rss.outputs.contentSnippet}}
          link: ${{on.rss.outputs.link}}
        run: |
          echo title: $title
          echo contentSnippet: $contentSnippet
          echo link: $link
```

こちらでは、`on`のところの定義が `rss` というものになっているらしい。この`rss`のトリガの部分がコミュニティベースで開発されていく部分なんだろうな。
テンプレートリポジトリの例では `on.rss.url` にRSS取得元URLが書かれており、このURLの内容がこのワークフロー上で処理されるみたい。実行している処理はフィードから`title`とかの要素を取ってきて`echo`してるだけ。

https://github.com/orta-contrib/actionsflow-workflow-sample/runs/1298845151?check_suite_focus=true#step:5:7
![first-run](/images/20201023/first-run.png)

#### Workflowの追加

実行方法はわかったので、自分でワークフローを追加してみました。

トリガ一覧を眺めて、簡単そうな`API polling`のワークフローを組んでみることに。
[Actionsflow Triggers | Actionsflow Documentation](https://actionsflow.github.io/docs/triggers/#official-triggers)

（`Twitter`使いたかったけど、`CONSUMER_KEY`とかの取得が面倒なので...）

作成したワークフローファイルは[こちら](https://github.com/orta-contrib/actionsflow-workflow-sample/blob/main/workflows/ghibli.yml)。

```yaml:ghibli.yml
on:
  poll:
    url: https://ghibliapi.herokuapp.com/locations?limit=5
    config:
      limit: 5
jobs:
  print:
    name: Print
    runs-on: ubuntu-latest
    steps:
      - name: Print Outputs
        env:
          name: ${{ on.poll.outputs.name }}
          terrain: ${{ on.poll.outputs.terrain }}
        run: |
          echo "name: $name"
          echo "terrain: $terrain"
```

なんか`rss`とあんま変わらないですね...。
ちょっとハマったのが、JSONを返すAPIでもArray形式で返してくれるAPIじゃないとエラーが出た点(`[{..},...]`はOKで、`{...}`というのはダメ)。`e.forEach is not function`というエラーだったので、`on.poll.outputs`あたりの指定の仕方次第なのかもしれない。GitHub Actionsあまり分かってない。

**追記:**

https://youtu.be/nZiP10uqdvE?t=2014

見返してて、ここで `${{ toJSON(on.poll.outputs.raw__body) }}` を使っておけば、Array形式でないJSONも扱えるんじゃ無いかと思った。

**再追記:**

https://github.com/orta-contrib/actionsflow-workflow-sample/runs/1301386971?check_suite_focus=true#step:3:224

そんなことなかった。

### Impression

* 使い始めはテンプレートリポジトリからリポジトリを作成するだけなので本当に手軽。
  * テンプレートリポジトリからリポジトリを作成した後に、テンプレートリポジトリの更新を取り込む仕組みを用意しているのは優しい
  * 5分ごとにActionsが実行されるようになるので、お試しだけならリポジトリの設定からActionsを無効にするのを忘れずに
* ドキュメントもしっかり書かれていて頑張ってる感ある
* まだまだ情報が少ないので、自分がやりたいワークフローを組むだけならハードルが高いなと感じる気がする
* ワークフローの実行基盤を [act](https://github.com/nektos/act) に依存してるけど、actがどれだけGitHub Actionsとの互換性を維持できるのかというところが少し不安材料か
* コミュニティベースのトリガがどれだけ増えるかというのも重要な点だと思うけど、実際多くの人に使われるのは同じワークフローな気もするので、誰にとっても便利なゴールデンパターンが見つかるかどうかが鍵な気がする

* 吐息が入るの良くない。

## Contribution

特になし