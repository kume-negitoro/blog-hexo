---
title: iPhone3GをAirPlayスピーカーにする
date: 2020-09-03 22:14:51
tags:
    - Audio
    - AirPlay
    - Android
    - iPhone
    - iPhone3G
    - Jailbreak
    - shairport
---

お久しぶりです。なかなか書く気にならなかったのでだいぶ時間が空いてしまいました。
このブログは Hexo を使って書いていますが、はてブロみたいにさらっと公開できないのでネタがあっても面倒であんまり書こうと思えないのが微妙ですね。
そもそもこのブログは勉強のために作ったものなので、そろそろ Nuxt とか Next なんかを使って作り直したいところです。


## 本題
今回は発掘した iPhone3G を AirPlay スピーカーにしてみたので、その記録として残します。

### 探す
結構記事があったりアプリがあったりするんですが、そもそもの話古い iOS には対応していないので、今回発掘したような化石には残念ながら使えません...。

というわけで古い記事を探すことになるのですが、まあこの頃の iPhone に対応しているようなアプリはまず脱獄が前提です。今回は sn0wbreeze で脱獄済みなので割愛しますが、やってみようという方は sn0wbreeze か redsn0w などで脱獄しておくとよいでしょう。

まずは Cydia で探してみますが、こちらにある AirServer なども i0S4.3 や iOS5 からの対応ばかりで惜しくも使えません(iPhone3G は iOS4.2.1 が最後)。3GS なら使えたのに、残念。

(少し前に、色々なリポジトリの提供元が Cydia から撤退するようなニュースをみましたが、そういえばどうなったんでしょうね。BigBoss はまだ生きていました)

さらに調査してみますが、AirServer の旧版アーカイブなどもリンク切れでなかなか見つかりません。[airtunesd](http://newosxbook.com/ent.jl?osVer=iOS11&exec=airtunesd)なるアプリも見つけましたが、こちらもバーション的に対応していませんでした(一応リンクを残しておきます。注意喚起ですが、こういうものを気軽に試すのはセキュリティ的にあまりお勧めしません)。


### 見つける
というわけで最終的に（奇跡的に？）たどり着いたのが[こちら](https://github.com/trenskow/AirFloat/issues/6)です。

AirFloat というアプリのリポジトリなのですが、issue で親切にも[バイナリ](http://sourceforge.net/projects/airfloatdhfork/files/latest/download)を配布してくれている方がいました。iOS4.2.1 に対応しているようで、iPodTouch2G や 5G でも動くようです。

一応、[修正版](http://sourceforge.net/projects/airfloatdhfork/files/AirFloat_c.zip/download)なるものもアップロードされているようなのでこちらの方がいいかもしれません(違いがよくわかりませんが)。


### インストール
zip を落としてきたら、解凍して AirFloat.app があることを確認します。次に、なんらかの手段で iPhone の/Applications に転送します(私は iFunBox を使いました)。

そうしたら今度は、AirFloat.app/AirFloat に実行権限を付与します。ここで注意ですが、AirFloat.app ではなく、内部の AirFloat(拡張子なし)に権限を付与します。

こちらもなんらかの手段で権限を設定しましょう。iFile でつけられるとのことだったので iFile を試しましたが、うまく動かなかったので今回は MobileTerminal で設定します。MobileTerminal は iOS3 から対応しているようですね、Whited00r でも安心。iFile や SSH でも良いでしょう。

MobileTerminal を立ち上げたら、早速 root になります。パスワードは変更していなければ alpine です。

```
$ su
Password:
```

そうしたら実行権限を付与します。

```
$ chmod 755 /Applications/AirFloat.app/AirFloat
```

ここまできたらおきまりの再起動&Respring ですね。

もし再起動後にアプリを立ち上げてすぐに落ちてしまうようなら、実行権限がうまく付与されていないかもしれません。落ち着いてもう一度操作しましょう。


### 使ってみる
AirFloat の使い方ですが、アプリを立ち上げるだけです。
注意点としては、スリープすると切れてしまうので、スリープしないように設定しておきましょう(アプリ内の設定に、使っているときはスリープしない、という項目があるのですが、こちらはうまく動作しませんでした。環境によるかもしれません)。

アプリを立ち上げると、iTunes などで AirPlay 一覧に登場し、ストリーミングできます。お疲れ様でした。


## 感想
正直重いのでメインで使えるかというと微妙なところです（頻繁に音が途切れる）。issue ではマルチタスクが有効になっていると途切れやすいという報告があったので、マルチタスクを無効にした ipsw を焼くのがよいかもしれません。

ここまで紹介しておいて身も蓋もない話ですが、適当な Android 端末に[AirplayReceiver](https://play.google.com/store/apps/details?id=com.agileapps.airplay&hl=ja)を入れたほうが快適です。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">余ってるAndroid端末にAirPlayReceiverを入れて適当なコンポに繋ぐと手軽にネットワークスピーカーが作れます</p>&mdash; ねぎまぐろ (@kume_negitoro) <a href="https://twitter.com/kume_negitoro/status/1301418093747425280?ref_src=twsrc%5Etfw">September 3, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

ただ iPhone3G でも動くというところに価値があると思いますので、どうしても有効活用したかった、という方や暇な方にはお勧めです。特に、昔の iPod や iPhone 対応の Dock スピーカーを生かしたいという方にはかなり向いているんじゃないかなあ、と思います。

---

そういえばこのために、[DoubleTwist](https://play.google.com/store/apps/details?id=com.doubleTwist.androidPlayer&hl=ja)の有料版を購入しました（？）。
DoubleTwist は有料の拡張を入れると Android からでも AirPlay で音楽を飛ばせるようになります。昔は微妙だった記憶がありますが、今は普通に使えました。Android から AirPlay したい人は素直にこちらを購入するとよいでしょう。

ちなみに DoubleTwist から AirPlayReceiver に音楽を飛ばすこともできます。Apple 製品が一切介在しない AirPlay とは一体...。
