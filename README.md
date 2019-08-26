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

### piユーザーのオートログインを無効化

`autologin-user=pi`をコメントアウトする

```
$ sudo vim /etc/lightdm/lightdm.conf
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
