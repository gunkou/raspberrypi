# raspberrypi

[ドキュメント](https://www.raspberrypi.org/documentation/)

## インストール

[Raspberry Pi Downloads – Software for the Raspberry Pi](https://www.raspberrypi.org/downloads/)


## インストール後にやること

ラズベリーパイの「ファームウェア」を最新版

```
sudo rpi-update
```

再起動

```
sudo reboot
```

パッケージ管理ツールを最新版

```
sudo apt-get update
```

アップグレード

```
sudo apt-get upgrade
```

vimのインストール

```
sudo apt-get install vim
```

## セキュリティ対策
### rootのパスワードを設定

```
$ sudo passwd root
```

設定したパスワードでrootユーザーにログインできるか確認

```
$ su
```

設定したパスワードでrootユーザーに切り替えられたら元のユーザーに戻る

```
$ exit
```

### 新規ユーザーの追加

`{{ newuser }}`は任意のユーザー名

ユーザーの追加

```
$ sudo adduser {{ newuser }}
```

piユーザーのgroupを確認

```
$ groups pi
```

piユーザーと同じgroupに追加ユーザーを追加させる

```
$ sudo usermod -G {{ 先ほどの実行したgroup }} {{ newuser }}
```

`groups pi`と同じになっているか確認

```
$ groups {{ newuser }}
```

piユーザー内のファイルを、追加ユーザーにコピー

```
$ sudo cp -r /home/pi/* /home/{{ newuser }}
```

### piユーザーのオートログインを無効化して追加ユーザーに変更する

```
$ sudo vim /etc/lightdm/lightdm.conf
```

以下をコメントアウトする
```
autologin-user=pi
```

新しいユーザー名に書き換える

```
$ sudo vim /etc/systemd/system/autologin@.service
```

```:autologin@.service
ExecStart=-/sbin/agetty --autologin pi --noclear %I $TERM
↓
ExecStart=-/sbin/agetty --autologin {{ newuser }} --noclear %I $TERM
```

```
$ sudo vim /etc/systemd/system/getty@tty1.service.d/autologin.conf
```

```:autologin.conf
ExecStart=-/sbin/agetty --autologin pi --noclear %I 38400 linux
↓
ExecStart=-/sbin/agetty --autologin {{ newuser }} --noclear %I 38400 linux
```

再起動

```
$ sudo reboot
```

piユーザーが消えていたらOK

```
$ who
```

### piユーザーの削除

```
$ sudo userdel -r pi
```

確認

```
$ id -a pi
```

### SSH のポートを変更する

```
$ sudo vim /etc/ssh/sshd_config
```

```
Port XXXXX # 49152〜65535の中から好きに選ぶ
```

SSHを再起動

```
$ sudo /etc/init.d/ssh restart
```

`[OK]`が表示されたらログアウトしてポート番号を指定してSSH接続できるか確認


### rootユーザーで直接ログイン出来なくする

```
$ sudo vim /etc/ssh/sshd_config
```

以下のコメントアウトを削除してrootログインにおいて、すべてのインタラクティブな認証を禁止し、公開鍵もしくはホストベース、GSSAPIによる認証のみが有効にする

```
#PermitRootLogin prohibit-password
```

## ローカルIP固定設定

> LANにおいて、Routerの設定によってはLANに属するコンピューターに動的にローカルIPが割り振られています。
> SSH接続はこのローカルIPを利用しているので、動的にローカルIPが変更されては接続できなくなってしまいます。
> なので、安定してRaspberry PiにSSH接続するためには固定的にローカルIPを設定する必要があります。

`man dhcpcd.conf` を実行して、`static value` のマニュアルにexampleがあるのでそのまま拝借します。


