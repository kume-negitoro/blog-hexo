---
title: Kindleについての覚書
date: 2021-08-12 16:35:02
tags:
---

Linux 環境で Kindle を使いたいなあと思ったので覚書です。


## Wineのインストール

Wine は普通に**wine64**と**winetricks**をインストールします。

$ `sudo apt install -y wine64 winetricks`


## フォントのインストール

これだけだと文字化けしてしまうので、winetricks を使って cjk フォントをインストールします。

$ `winetricks cjkfonts`


## Kindleのインストール

なんとなく旧バージョンの**1.17**を入れたくなったので**1.17**をインストールします。
ただ、Amazon 公式では既に配布が終了しているので、適当な野良サイトから落としてきます。

私は[ここ](https://kindle-for-pc.jp.uptodown.com/windows/versions)から**1.17.44170**を落としてきました。
野良サイトからバイナリを落としてくるときは忘れずにハッシュを確認します。
私は[ここ](https://www.mobileread.com/forums/showthread.php?t=283371)でハッシュを確認しました。

$ `sha256sum kindle-for-pc-1-17-44170.exe`
```
14e0f0053f1276c0c7c446892dc170344f707fbfe99b6951762c120144163200  kindle-for-pc-1-17-44170.exe
```

ハッシュが一致しているので OK ですね。

あとは普通にダブルクリックして wine からインストールします。
ただ、ネットワークに接続できませんと表示されてログインもできません。
これは古い証明書が消されているせいのようです。


## 証明書のインストール

私は LinuxMint を使っていますが、Linux の最近のバージョンでは消されている古い証明書があるようです。
Kindle 1.17 では旧 Symantec 社の証明書を使っていて、そのために接続が失敗しているようです。

(参考: https://twitter.com/sagawa_aki/status/1362372893716344834?s=20)

というわけで、[ここ](https://knowledge.digicert.com/ja/jp/solution/SO28752.html)から旧 Symantec の G5 ルート証明書をいただいて、手動で追加する必要があります。

適用な名前でファイルを作る必要があるので、コピー&ペーストして仮に**G5.crt**として保存します。

ファイルを作ったら、[こちらの記事](https://qiita.com/msi/items/9cb90271836386dafce3)を参考にして証明書を認識させます。

$ `sudo mkdir -p /usr/share/ca-certificates/mylocal/` 

$ `sudo cp /path/to/dir/G5.crt /usr/share/ca-certificates/mylocal/`

$ `sudo update-ca-certificates`

再度 Kindle を起動して、無事にネットワークに接続できれば OK です。


## kindlekeyを入手する

もしかしたら kindlekey 的なものが欲しくなるかもしれません。その場合は、Wine 環境に python2 系をインストールして、kindlekey[.]py (もしくは kindlekey[.]pyw)を実行する必要があります。

まずはここから[python2をダウンロード](https://www.python.org/downloads/release/python-2718/)して、サクッとインストールします。

次に、どこかから **kindlekey[.]pyw** を見つけてきて、`$HOME/.wine/drive_c/`に配置します。

$ `wine cmd`

Z:\home\negitoro\.wine\drive_c>`python kindlekey.py`

もし **ImportError: No module named winreg** 的なものが出たら **winreg** を **_winreg** に置換して再度実行します。

**Key successfully retrieved to kindlekey1[.]k4i** と表示されたら終了です。