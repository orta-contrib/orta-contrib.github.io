---
title: "YT: ossf/criticality_score"
date: 2021-02-22T23:46:20+09:00
draft: false
toc: true
tags: ["youtube", "oss", "criticality_score"]
---

## YouTube

{{<youtube 7fFQCsgVXts>}}

## ossf/criticality_score

https://github.com/criticality_score

### why?

https://opensource.googleblog.com/2020/12/finding-critical-open-source-projects.html

Twitter で上記の記事が流れて来て、少し読んでみると OpenSource プロジェクトのスコアをつけると言うものだったので興味があった。また、スコア計算式の元ネタは Rob Pike 氏のものということでどんなスコアが出るのかちょっと見てみようと思った。

### what?

Open Source プロジェクトの活動履歴などを元に、そのプロジェクトの Criticality Score というものを算出する Python ツール。

スコアの算出に使用されるパラメータは[README](https://github.com/ossf/criticality_score#criticality-score)に記載があり、更新頻度、コントリビュータの数や所属組織への多様性、Issue に対する活動頻度などのパラメータに対し、それぞれ重みを付けて計算することで一元的な Criticality Score というものを算出している。Criticality とは何なのかが少しわかりづらく、実際そのような[Issue](https://github.com/ossf/criticality_score/issues/30)も立てられている。

また、現在の計算式も確定というわけではなく、Criticality Score V2 のアイデアについてもディスカッションが行われている。

https://github.com/ossf/criticality_score/issues/82

ユースケースとしては、あるコミュニティのプロダクトが依存している OSS プロジェクトに対して Criticality Score を算出し、Score の低いプロジェクトは継続性に難があると考えられるため、そこに対して依存を外したり、サポートを付けるなどの判断をするために使うことを目的としているように見える。

最近だと、Google が OSS の脆弱性データベースである OSV を公開するなど、OSS プロジェクトの健全性を測る仕組みが注目を集めており、その一端となることが期待されているように思う。

[Google、オープンソースソフトウェアの脆弱性をバージョンごとにデータベース化する「OSV」（Open Source Vulnerabilities）プロジェクトを開始](https://www.publickey1.jp/blog/21/googleosvopen_source_vulnerabilities.html)

### How?

スコア算出ツールは Python モジュールとして公開されているため、`pip`でインストールして実行するだけ。

```bash
$ pip3 install criticality-score
...
$ criticality_score --repo github.com/kubernetes/kubernetes
name: kubernetes
url: https://github.com/kubernetes/kubernetes
language: Go
created_since: 82
updated_since: 0
contributor_count: 3777
org_count: 5
commit_frequency: 96.6
recent_releases_count: 78
updated_issues_count: 5169
closed_issues_count: 2875
comment_frequency: 5.4
dependents_count: 435210
criticality_score: 0.98608
```

測定対象は GitHub と GitLab 上のプロジェクトだけであり、それぞれ実行前にアクセストークンを環境変数に設定する必要がある。

最終的な計算結果であるスコアは一番最後に出力されている `criticality_score: 0.98608` の部分。0~1 までの値を取り、`1`が最も高い（信頼できるプロジェクトである）ことを示す。
[`kubernetes`](https://gihtub.com/kubernetes/kubernetes)は、さすがほぼ最高値に近い。

出力形式を指定する`--format`オプションと、Criticality Score 算出に使用するパラメータを追加できる`--params`オプションも使える。

```bash
$ criticality_score -h
usage: criticality_score [-h] --repo REPO [--format {default,csv,json}] [--params PARAMS [PARAMS ...]]

Gives criticality score for an open source project

optional arguments:
  -h, --help            show this help message and exit
  --repo REPO           repository url
  --format {default,csv,json}
                        output format. allowed values are [default, csv, json]
  --params PARAMS [PARAMS ...]
                        Additional parameters in form <value>:<weight>:<max_threshold>
```

動かすのが面倒な場合は、サンプルとして実行結果データが公開されているのでそちらを確認するのが楽。

https://commondatastorage.googleapis.com/ossf-criticality-score/index.html

各言語ごとの Top 200 プロジェクトと、10 万プロジェクトに対する実行結果がある。
配信では 10 万プロジェクトに対する実行結果を見ていたが、`criticality_score=0.7`ぐらいまでなら OK で、`0.6`あたりのプロジェクトになると対処を考えておいた方がいいのかな、ぐらいの印象を受けた。

https://youtu.be/7fFQCsgVXts?t=2429

### Impression

多種多様の OSS プロジェクトが出てくる中、やはり俯瞰的に比較できる指標が求められる場面はあると考えられるため、このような指標は興味深い。しかし、実際にこのような値をどのような決断を下すときに参考にすべきなのか、また、スコア算出に使用するメトリクスは挙げられているものだけで良いのか、などは状況によって異なるためユースケースについてはいつまでも考えていかなくてはならないんだろう。

とりあえず、スコア算出自体は GitHub/GitLab プロジェクトであれば、パブリックでもオンプレミス環境でも簡単に実行できそうなので、ツールの存在は覚えておき、いろんな場面で試してみるのが良いのだろう。

## Contribute

特になし（アクセストークンの Scope 追記でいけるかと思ったけど勘違いだった...）
