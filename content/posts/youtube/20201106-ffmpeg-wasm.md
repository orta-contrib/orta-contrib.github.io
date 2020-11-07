---
title: "YT: ffmpeg.wasm"
date: 2020-11-07T22:46:20+09:00
draft: false
toc: true
tags: ["youtube", "ffmpeg.wasm", "ffmpeg", "wasm"]
---

## YouTube
{{<youtube zq6n4OWeV4Y>}}

## ffmpegwasm/ffmpeg.wasm
https://github.com/ffmpegwasm/ffmpeg.wasm

### Why?
今回はFFmpegをWebAssemblyで実装したというffmpeg.wasm。
Twitterで話題になってたのが興味を持ったきっかけ。WebAssemblyは気になってたけど、具体的にやりたいものも無いので触る機会を作るということもなかった。でも、ffmpegをブラウザで動かせるならのは何か用途ありそうだなーと思った。

### What?
FFmpegをWebAssemblyで実装したので、ブラウザ上でFFmpegを実行できるようになった。

ブラウザで実行する場合は、Webページに下記の`script`タグを追加するだけで使えるようになる。ただし、まだChromeでしか実行できないらしい。Safariで動かそうとしたらダメだった。あと、iOSのChromeでも動かなかった。

```javascript
<script src="https://unpkg.com/@ffmpeg/ffmpeg@0.9.4/dist/ffmpeg.min.js"></script>
<script>
  const { createFFmpeg } = FFmpeg;
  ...
</script>
```

また、Node.jsでもwasmを有効にするオプションを指定すれば使えるらしい。

### How?

もう少し詳細なコードがランディングページに載っているので、今回はそのコードを流用した。

https://ffmpegwasm.github.io/

ここに載ってるサンプルは、アップロードされたファイルをFFmpegで`mp4`形式に変換して、Web上で再生できるようにしたもの。

```javascript
  <script>
    const { createFFmpeg, fetchFile } = FFmpeg;
    // (1) Create ffmpeg object
    const ffmpeg = createFFmpeg({ log: true });
    const transcode = async ({ target: { files } }) => {
      const { name } = files[0];
      // (2) Load ffmpeg module
      await ffmpeg.load();
      // (3) Write file to ffmpeg.FS (FileSystem?)
      ffmpeg.FS('writeFile', name, await fetchFile(files[0]));
      // (4) Execute ffmpeg
      await ffmpeg.run('-i', name,  'output.mp4');
      // (5) Read converted file
      const data = ffmpeg.FS('readFile', 'output.mp4');
```

まず、`(1)`のように`ffmpeg`オブジェクトを生成し、`(2)`のようにロードする。
`(3)`で、アップロードされたファイルをffmpegのファイルシステムに一旦書き出している模様。おそらく、このファイルシステムに書き出されたファイルを対象にffmpegを実行している模様。
`(4)`で、ffmpegのコマンドを実行。実行コマンドはffmpegのものそのままっぽい（ffmpeg詳しく無いのでどの程度まで互換性があるのかはわかりません）。出力ファイルはffmpeg.FS上に書き出されているようなので、`(5)`のように読み出せば、ffmpegによって変換されたファイルを読み出せる。

このコードを少しいじって、アップロードした画像ファイルをモノクロに変換して表示するようにしてみました。

https://github.com/orta-contrib/ffmpeg.wasm-sample

GitHub Pages上でも普通に動いた。なるほど、便利。

https://orta-contrib.github.io/ffmpeg.wasm-sample/

![demo](/images/20201106/ffmpeg-wasm-on-ghpages.png)

### Impression
Chromeでしか動作しないというのは制約としては厳しいけど、そこがなければ本当に簡単に動作させられるし、GitHub Pagesのような静的サイトでも動作できるのはポータビリティ高くて素晴らしい。
WFHとかで動画の利用頻度は上がりそうなので、上手いこと定型作業化できれば便利に使えそう。

## Contribution
特になし。（ランディングページのwebp画像がSafariから読み出せなかったのが少しきになった）