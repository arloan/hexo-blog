title: 使用openssl生成RSA公私钥对
id: 80
categories:
  - 技术
  - 软件
date: 2013-12-16 15:10:18
tags:
  - 安全
  - RSA
---

实际上openssl只生成私钥，命令：
```
openssl genrsa -out rsakey.pem 2048
```

以上命令创建一个2048bit PEM 格式的RSA私钥，保存在文件 rsakey.pem 中。genrsa命令默认创建512bit的私钥，所以2048bit需要明确指定，bit长度必须是最后一个参数。-out参数指定保存文件名，省略的话会输出到命令行的标准输出，例如：
```
 $ openssl genrsa 1024
 Generating RSA private key, 1024 bit long modulus
 ..............................++++++
 ........++++++
 e is 65537 (0x10001)
 -----BEGIN RSA PRIVATE KEY-----
 MIICXAIBAAKBgQDAUOEf452OXmdyD+aUkMWXakk0ZKnN1jpnxI4VlDQed27MBGTp
 JWI/5BuWQy7c0Ppx89YaPJ9/MFMxhRFob9joqV50kqrMWJ/EYSVnqShywj/73OtR
 53PET7IEgCh1Sk/PkrC2IR3HV06v9nQE+SGW0iln948A8WEUz2mC5gxdJQIDAQAB
 AoGAX2P+tm2VSffDgc0nRwukJp2en/FKVIHtSGkzPLrdOztsZp/LWeWkHSLNGDRM
 u1Sg1QLocO0fA9x5d3eUkePgh3KuMJsphbLxyyMKkPl3+1MmS5ZU0DMfQTTuDmYt
 CPY7r9y6hgMR0yFeT/b/J3gb63d9se5dkPxkDCu6NQAAWSECQQDmRtBiD5vu3oTl
 qfCuHvtZ1RKuTay89XezF8MKdK19gGol8CccbLUCwXj/MWomnc3bJxnF5mkHn5ch
 jtmfbl5dAkEA1cyCawoUzofAFAcbsNI5LUMMbTqD+PBYgHphzzNnwnD0qf6qLOFY
 XPd8/3EMl/mkIRPC8Zj5XIt8b8VQSsG9aQJABIaPwd2cqu0kn37fhAyH1BQj8dkE
 9BG+8H4fDy2L21OSwnwdyKk6RXuX7SwDcg7X5N632vPUTJoOVlKAZGm+4QJAd+43
 vomyMESwN8vg+aiDkGNLJj6r50oTq0Z7dXq1mro4VN/ZYEGsisH96WDmGp5cNn3a
 XiJcx1Arpps9tiHF+QJBAKSANI6ZksruoZ/ZS7UN/FRd7pqTmNET+tesCmEmO2Wy
 1iiDuS5rWXmpGa+Sp7SAjF8+UtqVUXCpJhqAvPasagA=
 -----END RSA PRIVATE KEY-----
 $
```

以上从“`-----BEGIN RSA PRIVATE KEY-----`”到“`-----END RSA PRIVATE KEY-----`”的部分（含）就是PEM格式的RSA私钥了。

那么，需要公钥的话就可以从以上私钥计算出来（所以还是要保存到文件里），命令：

```
 $ openssl rsa -in rsa1024.pem -pubout
 writing RSA key
 -----BEGIN PUBLIC KEY-----
 MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDAUOEf452OXmdyD+aUkMWXakk0
 ZKnN1jpnxI4VlDQed27MBGTpJWI/5BuWQy7c0Ppx89YaPJ9/MFMxhRFob9joqV50
 kqrMWJ/EYSVnqShywj/73OtR53PET7IEgCh1Sk/PkrC2IR3HV06v9nQE+SGW0iln
 948A8WEUz2mC5gxdJQIDAQAB
 -----END PUBLIC KEY-----
 $
```

恩，当然可以在命令最后加上重定向自动保存为文件：
```
openssl rsa -in rsa1024.pem -pubout > rsa1024.pub
```