---
title: LinuxのCD取り込み事情
date: 2019-08-08 01:03:00
tags:
    - Linux
    - Audio
    - shnsplit
    - CD
    - cue
    - k3b
---

つい先日まで CD の取り込みには ExactAudioCopy を使っていました。

特にこいつに不満はなかったんですがメイン PC を Linux にしている現状、 CD を取り込むたびに Windows を立ち上げるのは面倒だと思っていたのでさっさと Linux で取り込める環境を作ってやるつもりでした。

さらに CD のデータはまるっと残しておきたいタイプの人間なので EAC 標準の .cue + .wav という形式でフルバックアップを取っていたわけですが、こいつが微妙に扱いにくい。

EAC では分割後にエンコードをかけてくれる機能がなかったのでフルバックアップを取ったあとに DAEMON Tools でマウントしてもう一度エンコード付きで取り込みなどという眠たくなりそうな作業をしていました（実際なった）。

さらに悪いことに元からだいぶ怪しい DAEMON Tools の挙動がアップデートのたびに悪化、実ディスクドライブと競合するなどのひどい状態だったこともあり Linux 環境への移行に拍車がかかりました。

---

前置きが長くなりましたが実際に用意した環境は以下です。

- LinuxMint 19
- k3b
- 自作バッチファイル

k3b は取り込みから CD 情報の取得、ライティングまで面倒を見てくれるグラフィカルでなかなか充実したソフトです。しかも .cue + .wav での取り込みができるので既存のバックアップをそのまま活用できます。

自作バッチファイルは .cue + .wav から flac でエンコードした曲情報付きのファイルを作るところまで担当します。

またディレクトリ構造は次のようになっています。

```
~/ミュージック
├── CD_backup
│   ├── 何かしらのCDデータ（.cue + .wav）
│   └── cdsplit <- これが自作バッチファイル
└── 何かしらのCDデータ（.flac） <- 自動で出力したい
```

## k3b のインストール

たしか[こちらのブログ](https://abyssluke.hatenablog.com/entry/2015/03/07/173259)を参考にさせていただきました。

```bash
$ sudo apt install k3b k3b-i18n language-pack-kde-ja kde-config-cddb
```


## バッチファイルの用意

shnsplit と cuetag を使います。

```bash
$ sudo apt install shntool cuetools
```

```bash
#!/bin/bash

DEST=../../$(basename "$(pwd)")
CUENAME=`find *.cue | head -n 1`
WAVNAME=`find *.wav | head -n 1`

shnsplit -f "$CUENAME" -o "flac flac -6 -o %f -" -t "%n %t - %p" "$WAVNAME"
cuetag "$CUENAME" *.flac
mkdir "$DEST"
mv *.flac "$DEST"

```

忘れずに実行権限をつけておきます。

```bash
$ chmod +x cdsplit
```

シェルスクリプトがあまり得意ではないので間違ってもキレイなコードにはなっていないと思いますが今回は動けば良しとします。

動作としては CD_backup 以下のあるディレクトリで `../cdsplit` を実行したときにミュージック以下へ同名のディレクトリを作成してエンコードしたファイルを流し込みます。

他に特に明記すべきことはなさそうですが shnsplit の行では .wav ファイルの分割とエンコード、 cuetag の行では曲情報の付与を行っています。


## エンコード

上記のディレクトリ構造に従ってバックアップファイルを置いたディレクトリで実行します。

```bash
~/ミュージック/CD_backup/何かしらのCDデータ$ ../cdsplit
```

これで今までどおり CD のバックアップとエンコードをする環境が構築できました。あとは適当にスマホや音楽プレイヤーにぶち込みます。