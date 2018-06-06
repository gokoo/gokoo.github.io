title: 比特币白皮书学习笔记
tags:
	- blockchain
	- 算法
	- 比特币
categories:
- blockchain
date: 2018/5/1 22:46:25
---
# 前言

## 索引

**地址** 

比特币地址（例如：1DSrfJdB2AnWaFNgSbv3MZC2m74996JafV）由一串字符和数字组成，以阿拉伯数字“1”开头。就像别人向你的email地址发送电子邮件一样，他可以通过你的比特币地址向你发送比特币。

**BIP**

比特币改进提议 （Bitcoin Improvement Proposals 的缩写），指比特币社区成员所提交的一系列改进比特币的提议。例如，BIP0021是一项改进比特币统一资源标识符（URI）计划的提议。

**比特币**

“比特币”既可以指这种虚拟货币单位，也指比特币网络或者网络节点使用的比特币软件。

**区块**

一个区块就是若干交易数据的集合，它会被标记上时间戳和之前一个区块的独特标记。区块头经过哈希运算后会生成一份工作量证明，从而验证区块中的交易。有效的区块经过全网络的共识后会被追加到主区块链中。

**区块链**

区块链是一串通过验证的区块，当中的每一个区块都与上一个相连，一直连到创世区块。

**确认**

当一项交易被区块收录时，我们可以说它有一次确认。矿工们在此区块之后每再产生一个区块，此项交易的确认数就再加一。当确认数达到六及以上时，通常认为这笔交易比较安全并难以逆转。

**难度**

整个网络会通过调整“难度”这个变量来控制生成工作量证明所需要的计算力。

**难度目标**

使整个网络的计算力大致每10分钟产生一个区块所需要的难度数值即为难度目标。

**难度调整**

整个网络每产生2,106个区块后会根据之前2,106个区块的算力进行难度调整。

**矿工费**

交易的发起者通常会向网络缴纳一笔矿工费，用以处理这笔交易。大多数的交易需要0.5毫比特币的矿工费。

**哈希**

二进制数据的一种数字指纹。

**创世区块**

创世区块指区块链上的第一个区块，用来初始化相应的加密货币。

**矿工**

矿工指通过不断重复哈希运算来产生工作量证明的各网络节点。

**网络**

比特币网络是一个由若干节点组成的用以广播交易信息和数据区块的P2P网络。

**工作量证明**

工作量证明指通过有效计算得到的一小块数据。具体到比特币，矿工必须要在满足全网目标难度的情况下求解SHA256算法。

**奖励**

每一个新区块中都有一定量新创造的比特币用来奖励算出工作量证明的矿工。现阶段每一区块有25比特币的奖励。

**私钥**

用来解锁对应（钱包）地址的一串字符，例如5J76sF8L5jTtzE96r66Sf8cka9y44wdpJjMwCxR3tzLh3ibVPxh。

**交易**

简单地说，交易指把比特币从一个地址转到另一个地址。更准确地说，一笔“交易”指一个经过签名运算的，表达价值转移的数据结构。每一笔“交易”都经过比特币网络传输，由矿工节点收集并封包至区块中，永久保存在区块链某处。

**钱包**

钱包指保存比特币地址和私钥的软件，可以用它来接受、发送、储存你的比特币。



# 第一章

## 什么是比特币

在一定意义上，比特币才是互联网货币的完美形态。因为它具有快捷、安全、无国界的特性。
完全虚拟的。没有实体，拥有密钥是使用比特币的唯一条件
<!--more-->
**挖矿**

——在比特币网络中成功写入一个区块交易——的难度是动态调整的，保证不管有多少矿工（多少CPU）挖矿，平均每10分钟只有一个矿工成功。

比特币协议还规定，每四年新币的开采量减半，同时限制比特币的最终开采总量为2,100万枚

**比特币由这些构成：**

▷ 一个去中心化的点对点网络（比特币协议） 

▷ 一个公共的交易账簿（区块链） 

▷ 一个去中心化的数学的和确定性的货币发行（分布式挖矿） 

▷ 一个去中心化的交易验证系统（交易脚本）

比特币的去中心化算法解决了以前的数字货币的短板，

**一个分布式计算问题的解决方案**

中本聪的此项发明，对“拜占庭将军”问题也是一个可行的解决方案，这是一个在分布式计算中未曾解决的问题。简单来说，这个问题包括了试图通过在一个不可靠、具有潜在威胁的网络中，通过信息交流来达成一个行动协议共识。中本聪的解决方案是使用**工作量证明**的概念在没有中央信任机构下达成共识，这代表了分布式计算的科学突破，并已经超越了货币广泛的适用性。它可以用来达成去中心化的网络共识来**公正选举、彩票、资产登记，以及数字化公证**等等。

# 第2章 比特币的原理

![](http://zhibimo.com/read/wang-miao/mastering-bitcoin/Images/Fig201.png)

# 第3章 比特币客户端

[客户端命令行操作](http://zhibimo.com/read/wang-miao/mastering-bitcoin/Chapter03.html)

# 第4章 密钥、地址、钱包

## 4.1.2 私钥和公钥
一个比特币钱包中包含一系列的密钥对，每个密钥对包括一个私钥和一个公钥。私钥（k）是一个数字，通常是随机选出的。有了私钥，我们就可以使用椭圆曲线乘法这个单向加密函数产生一个公钥（K）。有了公钥（K），我们就可以使用一个单向加密哈希函数生成比特币地址（A）
![](http://zhibimo.com/read/wang-miao/mastering-bitcoin/Images/Fig401.png)

使用比特币核心客户端生成一个新的密钥,使用getnewaddress命令。出于安全考虑，命令运行后只显示生成的公钥，而不显示私钥。如果要bitcoind显示私钥，可以使用dumpprivkey命令。dumpprivkey命令会把私钥以Base58校验和编码格式显示，这种私钥格式被称为钱包导入格式（WIF，Wallet Import Format）

## 4.1.4 公钥

通过**椭圆曲线算**法可以从私钥计算得到公钥，这是不可逆转的过程：K = k * G 。其中k是私钥，G是被称为生成点的常数点，而K是所得公钥。其反向运算，被称为“寻找离散对数”——已知公钥K来求出私钥k——是非常困难的，就像去试验所有可能的k值，即暴力搜索。

## 4.1.5 椭圆曲线密码学解释

椭圆曲线加密法是一种基于离散对数问题的非对称（或公钥）加密法，可以用对椭圆曲线上的点进行加法或乘法运算来表达。
![](http://zhibimo.com/read/wang-miao/mastering-bitcoin/Images/Fig402.png)

比特币使用了secp256k1标准所定义的一条特殊的椭圆曲线和一系列数学常数。该标准由美国国家标准与技术研究院（NIST）设立。secp256k1曲线由下述函数定义，该函数可产生一条椭圆曲线：
```
y2 = (x3 + 7)} over (Fp)
或
y2 mod p = (x3 + 7) mod p
```
上述mod p（素数p取模）表明该曲线是在素数阶p的有限域内，也写作Fp，其中p = 2256 – 232 – 29 – 28 – 27 – 26 – 24 – 1，这是一个非常大的素数。

## 啥是椭圆曲线
[一篇关于椭圆曲线的文章](http://mp.weixin.qq.com/s/jOcVk7olBDgBgoy56m5cxQ)


二元一次方程表示直线，二元二次方程表示圆锥曲线(圆，椭圆，双曲线和抛物线)，那么二元三次方程表示什么曲线呢？答案自然就是椭圆曲线。为了方便研究，大部分的二元三次方程可以简化成魏尔斯特拉斯方程的形式，见公式(4)。其中，系数a 和b 需要满足条件4a3 + 27b2 ≠ 0，该条件保证方程中不会出现非奇异点以获得平滑的椭圆曲线。
```
ax + by + z = 0 (1)
ax2 + by2 + cxy + dx + ey + f = 0 (2)
ax3 + bx2y + cxy2 + dy3 + ex2 + fxy + gy2 + hx + iy + j = 0 (3)
y2 = x3 + ax + b (4)
```
一个违反直觉的事实是：椭圆曲线的形状跟椭圆毫无关系。当初数学家们在研究如何计算椭圆弧长的时候发现需要求解如下类型的积分, 由于和椭圆相关，积分中的分母项y =√(x3+ax+b) 便被称作椭圆曲线。

## 4.2 比特币地址

比特币地址是一个由数字和字母组成的字符串，可以与任何想给你比特币的人分享。由公钥（一个同样由数字和字母组成的字符串）生成的比特币地址以数字“1”开头。下面是一个比特币地址的例子：
```
1J7mdg5rbQyUHENYdx39WVWK7fsLpEoXZy
```
在交易中，比特币地址通常以收款方出现。如果把比特币交易比作一张支票，比特币地址就是收款人，也就是我们要写入收款人一栏的内容。一张支票的收款人可能是某个银行账户，也可能是某个公司、机构，甚至是现金支票。支票不需要指定一个特定的账户，而是用一个普通的名字作为收款人，这使它成为一种相当灵活的支付工具.

比特币地址与公钥不同。比特币地址是由公钥经过单向的哈希函数生成的。


![](http://zhibimo.com/read/wang-miao/mastering-bitcoin/Images/Fig405.png)

## 4.3 用Python实现密钥和比特币地址(bitcoin库)

```python
import bitcoin

# Generate a random private key
valid_private_key = False while not valid_private_key:
    private_key = bitcoin.random_key()
    decoded_private_key = bitcoin.decode_privkey(private_key, 'hex')
    valid_private_key =  0 < decoded_private_key < bitcoin.N

print "Private Key (hex) is: ", private_key
print "Private Key (decimal) is: ", decoded_private_key

# Convert private key to WIF format
wif_encoded_private_key = bitcoin.encode_privkey(decoded_private_key, 'wif')
print "Private Key (WIF) is: ", wif_encoded_private_key

# Add suffix "01" to indicate a compressed private key
compressed_private_key = private_key + '01'
print "Private Key Compressed (hex) is: ", compressed_private_key

# Generate a WIF format from the compressed private key (WIF-compressed)
wif_compressed_private_key = bitcoin.encode_privkey(
    bitcoin.decode_privkey(compressed_private_key, 'hex'), 'wif')
print "Private Key (WIF-Compressed) is: ", wif_compressed_private_key

# Multiply the EC generator point G with the private key to get a public key point
public_key = bitcoin.base10_multiply(bitcoin.G, decoded_private_key) print "Public Key (x,y) coordinates is:", public_key

# Encode as hex, prefix 04
hex_encoded_public_key = bitcoin.encode_pubkey(public_key,'hex') print "Public Key (hex) is:", hex_encoded_public_key

# Compress public key, adjust prefix depending on whether y is even or odd
(public_key_x, public_key_y) = public_key if (public_key_y % 2) == 0:
    compressed_prefix = '02' 
else:
    compressed_prefix = '03'
hex_compressed_public_key = compressed_prefix + bitcoin.encode(public_key_x, 16) print "Compressed Public Key (hex) is:", hex_compressed_public_key
# Generate bitcoin address from public key
print "Bitcoin Address (b58check) is:", bitcoin.pubkey_to_address(public_key)

# Generate compressed bitcoin address from compressed public key
print "Compressed Bitcoin Address (b58check) is:", \        bitcoin.pubkey_to_address(hex_compressed_public_key)
```
### 运行结果
```
$ python key-to-address-ecc-example.py
Private Key (hex) is:
 3aba4162c7251c891207b747840551a71939b0de081f85c4e44cf7c13e41daa6
Private Key (decimal) is:
 26563230048437957592232553826663696440606756685920117476832299673293013768870
Private Key (WIF) is:
 5JG9hT3beGTJuUAmCQEmNaxAuMacCTfXuw1R3FCXig23RQHMr4K
Private Key Compressed (hex) is:
 3aba4162c7251c891207b747840551a71939b0de081f85c4e44cf7c13e41daa601
Private Key (WIF-Compressed) is:
 KyBsPXxTuVD82av65KZkrGrWi5qLMah5SdNq6uftawDbgKa2wv6S
Public Key (x,y) coordinates is:
 (41637322786646325214887832269588396900663353932545912953362782457239403430124L,
 16388935128781238405526710466724741593761085120864331449066658622400339362166L)
Public Key (hex) is:
 045c0de3b9c8ab18dd04e3511243ec2952002dbfadc864b9628910169d9b9b00ec↵
243bcefdd4347074d44bd7356d6a53c495737dd96295e2a9374bf5f02ebfc176
Compressed Public Key (hex) is:
 025c0de3b9c8ab18dd04e3511243ec2952002dbfadc864b9628910169d9b9b00ec
Bitcoin Address (b58check) is:
 1thMirt546nngXqyPEz532S8fLwbozud8
Compressed Bitcoin Address (b58check) is:
 14cxpo3MBCYYWCgF74SWTdcmxipnGUsPw3
```
## 4.3 用Python实现密钥和比特币地址(椭圆曲线)

```python
import ecdsa
import random
from ecdsa.util import string_to_number, number_to_string

# secp256k1, http://www.oid-info.com/get/1.3.132.0.10
_p = 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEFFFFFC2FL
_r = 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141L
_b = 0x0000000000000000000000000000000000000000000000000000000000000007L
_a = 0x0000000000000000000000000000000000000000000000000000000000000000L
_Gx = 0x79BE667EF9DCBBAC55A06295CE870B07029BFCDB2DCE28D959F2815B16F81798L
_Gy = 0x483ada7726a3c4655da4fbfc0e1108a8fd17b448a68554199c47d08ffb10d4b8L
curve_secp256k1 = ecdsa.ellipticcurve.CurveFp(_p, _a, _b)
generator_secp256k1 = ecdsa.ellipticcurve.Point(curve_secp256k1, _Gx, _Gy, _r)
oid_secp256k1 = (1, 3, 132, 0, 10)
SECP256k1 = ecdsa.curves.Curve("SECP256k1", curve_secp256k1, generator_secp256k1,
oid_secp256k1)
ec_order = _r

curve = curve_secp256k1
generator = generator_secp256k1

def random_secret():
    random_char = lambda: chr(random.randint(0, 255))
    convert_to_int = lambda array:     int("".join(array).encode("hex"), 16) 
    byte_array = [random_char() for i in range(32)]
    return convert_to_int(byte_array)

def get_point_pubkey(point): 
    if point.y() & 1:
        key = '03' + '%064x' % point.x() 
    else:
        key = '02' + '%064x' % point.x() 
    return key.decode('hex')

def get_point_pubkey_uncompressed(point): 
    key='04'+\
        '%064x' % point.x() + \
        '%064x' % point.y() 
    return key.decode('hex')

# Generate a new private key.
secret = random_secret() 
print "Secret: ", secret

# Get the public key point.
point = secret * generator 
print "EC point:", point

print "BTC public key:", get_point_pubkey(point).encode("hex")

# Given the point (x, y) we can create the object using:
point1 = ecdsa.ellipticcurve.Point(curve, point.x(), point.y(), ec_order) 
assert point1 == point
```
### 运行结果
```c++
running the ec_math.py script
$ # Install Python PIP package manager
$ sudo apt-get install python-pip
$ # Install the Python ECDSA library
$ sudo pip install ecdsa
$ # Run the script
$ python ec-math.py
Secret:
38090835015954358862481132628887443905906204995912378278060168703580660294000
EC point:
(70048853531867179489857750497606966272382583471322935454624595540007269312627,
105262206478686743191060800263479589329920209527285803935736021686045542353380)
BTC public key: 029ade3effb0a67d5c8609850d797366af428f4a0d5194cb221d807770a1522873
```

