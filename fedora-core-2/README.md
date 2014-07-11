### MEMO

FedoraCore2 というレガシーなものを vagrant で動かす苦行。

virtualbox だけ作成できます。

```
PACKER_LOG=1 packer build -only=virtualbox-iso template.jso
```

sudo のバージョンアップをしておかないと vagrant で具合が悪いので上げる。
ただしwaitng for ssh な状態のうちに手動で… 自動化できそうだが…

```
scp -P 4391 tmp/sudo-1.6.9p23-1.i386.rpm vagrant@localhost:/tmp/
ssh localhost -p 4391 -l vagrant
sudo rpm -U /tmp/sudo-1.6.9p23-1.i386.rpm
rm /tmp/sudo-1.6.9p23-1.i386.rpm
exit
```

waitng for ssh な状態が続くので、
こっそり ssh ログインして下記を流すと packer から ssh ログインが正常に出来るようになる。FC3 と悪魔合体。
なぜか ks.cfg 内では正常に動作してくれない.よくわからん。

```
ssh localhost -p 4391 -l vagrant
/usr/bin/wget --quiet -P /tmp/ http://ftp.riken.jp/Linux/fedora/core/3/i386/os/Fedora/RPMS/libselinux-1.17.14-1.i386.rpm
/usr/bin/wget --quiet -P /tmp/ http://ftp.riken.jp/Linux/fedora/core/3/i386/os/Fedora/RPMS/openssh-3.9p1-7.i386.rpm
/usr/bin/wget --quiet -P /tmp/ http://ftp.riken.jp/Linux/fedora/core/3/i386/os/Fedora/RPMS/openssh-clients-3.9p1-7.i386.rpm
/usr/bin/wget --quiet -P /tmp/ http://ftp.riken.jp/Linux/fedora/core/3/i386/os/Fedora/RPMS/openssh-server-3.9p1-7.i386.rpm
sudo /bin/rpm --quiet -U /tmp/libselinux-*.rpm /tmp/openssh-*.rpm
rm /tmp/libselinux-*.rpm /tmp/openssh-*.rpm
exit
```

出来上がった box を利用すると、vagrant halt が sudo バージョンの影響で利用できないので下記で対応。

```
vagrant ssh dns -- 'sudo /sbin/shutdown -h now && echo halt'
```
