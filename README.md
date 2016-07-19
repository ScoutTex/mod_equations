#模线性方程组在加解密中的应用

By 55_AA @ 12:50 2016/7/19
##0 前言
在很多的加密算法中都用到了求模运算，结合实际运算中的一些已知条件，最终可将破解归结为模线性方程组的求解问题。本文重点探讨了在不考虑约束条件下，单模多元线性方程组的通用解法。这对研究算法的抗攻击性有一定的实际意义。

##1 概念和定义
**“线性方程组”**即普通的线性方程组，其解法在线性代数中有详细的讨论。比较适合计算机求解的就是高斯消元法。本文的也是在高斯消元法的基础上进行扩展。“线性方程组”的一般形式为：

        A*X=B

        其中：
            / a11, a12, ..., a1m \          / x1 \          / b1 \
            | a21, a22, ..., a2m |          | x2 |          | b2 |
        A = | a31, a32, ..., a3m |      X = | x3 |      B = | b3 | 
            |       ......       |          | ...|          | ...|
            \ an1, an2, ..., anm /          \ xn /          \ bn /
        
        A为系数矩阵，X为解向量，B为常数向量。


**“模线性方程”**也叫一次同余方程，其解法在初等数论中有详细的讨论，关键是清楚几个定理。这里列举出来，主要是这几个概念中文很容易混淆。

+ *欧拉函数：　　　　　Euler's totient function*
+ *欧拉定理：　　　　　Euler's theorem*
+ *费马小定理：　　　　Fermat's little theorem*
+ *欧几里德算法：　　　Euclidean algorithm*
+ *扩展欧几里德算法：　Extended Euclidean algorithm*

本文用到几个表示方法：

+ 最大公约数：　　　　gcd(a,b)表示a和b的最大公约数
+ 模逆：　　　　　　　inv(a,q)表示a关于q的逆元


“模线性方程”的一般形式为：

        a*x ≡ b(mod q)

**“模线性方程组”**的分类：

+ 按照未知数的数量区分：

    一元模线性方程组，如下，只有一个未知数x：

            a1*x ≡ b1(mod q1)
            a2*x ≡ b2(mod q2)
                  ...
            an*x ≡ bn(mod qn)
            其中n为正整数。

    多元模线性方程组，如下，未知数为x1、x2、...xm：

            a11*x1 + a12*x2 + ... a0m*xm ≡ b1(mod q1)
            a12*x1 + a22*x2 + ... a1m*xm ≡ b2(mod q2)
                  ...
            a1n*x1 + an2*x2 + ... anm*xm ≡ bn(mod qn)
            其中m、n为正整数。

+ 按照模的数量区分：

    单模线性方程组，如下，模数都为q：

            a11*x1 + a12*x2 + ... a0m*xm ≡ b1(mod q)
            a12*x1 + a22*x2 + ... a1m*xm ≡ b2(mod q)
                  ...
            a1n*x1 + an2*x2 + ... anm*xm ≡ bn(mod q)
            其中m、n为正整数。

    多模线性方程组，如下，模数为q1、q2、...qn：

            a1*x ≡ b1(mod q1)
            a2*x ≡ b2(mod q2)
                  ...
            an*x ≡ bn(mod qn)
            其中n为正整数。

综合考虑上述分类，模线性方程组可包括：

1. 一元单模线性方程组(A)，无太多实际意义。

2. 一元多模线性方程组(B)，当(q0、q1、...qn)两两互质时，方程组有唯一解。可通过中国剩余定理求解，这里不再详述。

3. 多元单模线性方程组(C)，是本文要研究的对象，它有多个未知数，且模数为同一个。这里的模数不要求必须为质数，因而更具有通用性。以下讨论都针对于这种形式展开。

4. 多元多模线性方程组(D)，在论文<sup>[1]</sup>中有详细论述，本文不做探讨。

##2 方程组解的求解过程

多元单模线性方程组(C)

            a11*x1 + a12*x2 + ... a0m*xm ≡ b1(mod q)
            a12*x1 + a22*x2 + ... a1m*xm ≡ b2(mod q)
                  ...
            a1n*x1 + an2*x2 + ... anm*xm ≡ bn(mod q)
            其中m、n为正整数。

可表示为：

        A*X ≡ B (mod q)
        
        其中：
            / a11, a12, ..., a1m \          / x1 \          / b1 \
            | a21, a22, ..., a2m |          | x2 |          | b2 |
        A = | a31, a32, ..., a3m |      X = | x3 |      B = | b3 | 
            |       ......       |          | ...|          | ...|
            \ an1, an2, ..., anm /          \ xn /          \ bn /
        
        A为系数矩阵，X为解向量，B为常数向量，q为模数。

在论文<sup>[1]</sup>中也探讨了该类方程组的解法。在该文中，当模为质数时采用高斯消元，当模为合数时采用LU分解。但其解法仍有一定的局限性，包括：

1. 当模为合数时，如果系数与模不互质则不能进行LU分解；
2. 只对系数矩阵的秩小于未知数个数时的多解情况做了探讨，而且仅给出了通解的表达式；
3. 文中只对系数行列式与模数互质的情况作了讨论，实际上不互质时方程组也是可解的。

本文则讨论模数和系数为任意整数，方程数和未知数为任意个数时的通用情况，并对有限域内的多解情况进行了延伸，把上述2和3导致的多解情况统一考虑。此外，本文在有限域内遍历多解，这对研究算法攻击是有现实意义的。

###2.1 通用求解过程
本文求解的基本思路还是高斯消元。通过全主消元，把系数的增广矩阵化简为如下的上三角形式，然后逐一回代求得所有解。

            / a11, a12, a33, ..., a1m,   b1 \ 
            |   0, a22, a32, ..., a1m,   b2 |
            |   0,   0, a33, ..., a3m,   b3 |
            |             ......            |
            |   0,   0,   0, ..., ann,   bn |
            |   0,   0,   0, ...,   0,    0 |
            |             ......            |
            \   0,    ,   0, ...,   0，   0 /

在化简上三角的过程中所遵循的原则和代数方程组有所区别，不能完全套用代数方程组的消元法则。特别是单个方程不能和任意数做乘法/除法，乘法和除法只能在有条件下进行：

1. 当整数m与模数q互质时，m乘以方程和原方程同解；
2. 当整数m与模数q互质时，m的模逆乘以方程和原方程同解，相当于方程除以m和原方程同解；

除此之外，方程不能做乘法/除法，否则可能造成解扩散。能够保证同解的初等行列式变换只能是如下三种形式：

1. (H1)交换任意两行
2. (H2)交换任意两列（不包括常数项列）
3. (H3)某一行加另外一行（可加多次。在模q的有限域内，加减法是一致的，A-B ≡ A+kB(mod q)）

化简上三角流程如下：

1. 寻找和模数q互质的系数，通过H1和H2变换，把该系数放置到对角线上；
2. 如果1找不到，则通过H3化简寻找一个系数k，使得gcd(k, q)为最小（在查找过程中如果gcd(k, q)=1，可提前结束查找）；
3. 通过H1和H2变换，把系数k放置到对角线上；
4. 对每个方程做如上1、2、3的变换。

最后得到的情形可能为：

+ 形式1

            a  x  x  α
            0  b  x  β
            0  0  c  γ
+ 形式2

            a  x  x  α
            0  b  x  β
+ 形式3

            a  x  x  α
            0  b  x  β
            0  0  0  γ

形式1是唯一解或多解，形式2是一定是多解，形式3无解。对已形式2，可化为：

+ 形式4

            a  x  x  α
            0  b  x  β
            0  0  0  0

然后对形式4和形式1做统一处理，进行回代求解。

回代过程：
从对角线的最右下角开始，求解单个未知数，其形式可简化为ax ≡ b (mod q)的求解。
由初等数论定理可知，x有三种情况：

1. 当gcd(a,q)=1时，有唯一解x=b*inv(a)，其中inv(a)是a关于q的模逆；
2. 当gcd(a,q)=k>1，且gcd(k,b)=k时，有多解， x=(b/k)*inv(a/k)+n(q/k)，其中n为整数且0 <= n < k；
3. 当gcd(a,q)=k>1，且gcd(k,b)<k时（即k不能整除b），无解；
4. 特别地，当a=b=0时，也可用如上的方法求解。但实际其解是关于q的完全剩余系（0,1,2,...,q-1)。

把所有的得到的解进行逐一回代，可得到方程组的单解或多解的解向量。

###2.2 解数的判定

通过上面的回代过程可知，每次回代都可能出现多解的情况，而且解的个数在模q的有限域内是有限的，因此整个方程组的解向量在模q的有限域内也是有限多组的。在得到上三角等价方程组后，不需要回代就可以判定解向量的个数。遍历多解需要耗费存储资源，所需资源和解数呈线性关系，因此在实际运算时，如果解数超过阈值应终止回代，以防资源耗尽。

设方程组有n个未知数，上三角的对角线系数分别为a1、a2、...、an，则解向量数:

    p = gcd(a1,q)*gcd(a2,q)*...*gcd(an,q)

其数学证明用到如下定理<sup>[2]</sup>：

    方程:
    a1*x1 + a2*x2 + ... an*xn ≡ b(mod q)
    有解的充要条件是k=gcd(a1,a2, ..., an,q),k能整除b。解的个数为：
    p = b^(n-1)*k

证明过程略。

##3 Hill密码攻击
Hill密码是一种分组对称加密算法。该算法在已知明文情况下，抗攻击性较差。例如以下加密过程：

    明文P=[11,12,7,14,18,23,17,5,19]
    加密矩阵K=[
        [23,15,19],
        [13,25,17],
        [24,18,11]
    ]
    K左乘P得密文C=[20,16,11,15,9,3,21,19,5]

现已知明文P和密文C，并且推测加密矩阵的阶数为3，则可通过求解如下线性方程组来计算加密矩阵M。

    设加密矩阵K的行展开向量为：
        k'= {ki}, 1 <= i <= 9
    已知明文向量为：
        P = {pi}, 1 <= i <= 9
    已知密文向量为：
        C = {ci}, 1 <= i <= 9

则可得方程组：

    p1*k1 + p2*k2 + p3*k3 + 0 + 0 + 0 + 0 + 0 + 0 ≡ c1(mod 26)
    0 + 0 + 0 + p1*k4 + p2*k5 + p3*k6 + 0 + 0 + 0 ≡ c2(mod 26)
    0 + 0 + 0 + 0 + 0 + 0 + p1*k7 + p2*k8 + p3*k9 ≡ c3(mod 26)

    p4*k1 + p5*k2 + p6*k3 + 0 + 0 + 0 + 0 + 0 + 0 ≡ c4(mod 26)
    0 + 0 + 0 + p4*k4 + p5*k5 + p6*k6 + 0 + 0 + 0 ≡ c5(mod 26)
    0 + 0 + 0 + 0 + 0 + 0 + p4*k7 + p5*k8 + p6*k9 ≡ c6(mod 26)

    p7*k1 + p8*k2 + p9*k3 + 0 + 0 + 0 + 0 + 0 + 0 ≡ c7(mod 26)
    0 + 0 + 0 + p7*k4 + p8*k5 + p9*k6 + 0 + 0 + 0 ≡ c8(mod 26)
    0 + 0 + 0 + 0 + 0 + 0 + p7*k7 + p8*k8 + p9*k9 ≡ c9(mod 26)

代入已知，得到对应的增广矩阵为：

    [
    	[ 11,  12,   7,   0,   0,   0,   0,   0,   0,  20],
    	[  0,   0,   0,  11,  12,   7,   0,   0,   0,  16],
    	[  0,   0,   0,   0,   0,   0,  11,  12,   7,  11],
    	[ 14,  18,  23,   0,   0,   0,   0,   0,   0,  15],
    	[  0,   0,   0,  14,  18,  23,   0,   0,   0,   9],
    	[  0,   0,   0,   0,   0,   0,  14,  18,  23,   3],
    	[ 17,   5,  19,   0,   0,   0,   0,   0,   0,  21],
    	[  0,   0,   0,  17,   5,  19,   0,   0,   0,  19],
    	[  0,   0,   0,   0,   0,   0,  17,   5,  19,   5],
    ]

解方程得唯一解：

    k' = [23, 15, 19, 13, 25, 17, 24, 18, 11]

显然k'对应加密矩阵K。

由此可见，只要获得足够多的明密文对，就一定能得到唯一的加密矩阵。但在少量明密文对的情况下，方程组系数矩阵的行列式值可能与模数26不互质，从而导致方程组出现多解。


##4 DSA共模攻击
DSA(Digital Signature Algorithm)是一种签名算法。当两次签名使用相同的随机数时，可通过两次明、密文对推导出私钥，导致私钥泄漏。

    p：L bits长的素数。L是64的倍数，范围是512到1024；
    q：p - 1的160bits的素因子；
    g：g = h^((p-1)/q) mod p，h满足h < p - 1, h^((p-1)/q) mod p > 1；
    m：为原文
    其中：
    x：x < q，x为私钥 ；    
    y：y = g^x mod p ，( p, q, g, y )为公钥；
    签名过程为：
    r = ( g^k mod p ) mod q
    s = ( inv(k) * (HASH(m) + xr)) mod q
    签名结果是( m, r, s )

在上述签名过程中，若两次签名使用相同的随机数k，两次的签名分别是：

    对原文m1的签名：(m1, r1, s1)
    对原文m2的签名：(m2, r2, s2)

则有：

    令: M=HASH(m)   
    由: s ≡ inv(k)*(M+x*r) (mod q) 
        k*inv(k) ≡ 1 (mod q)
    得: k*s ≡ K*inv(k)*(M+x*r) (mod q) 
    即: k*s ≡ M+x*r (mod q) 
    由两次签名的结果，可得方程组: 
        k*s1 ≡ M1 + r1*x (mod q) 
        k*s2 ≡ M2 + r2*x (mod q) 
    规范一下: 
        s1*k - r1*x ≡ M1 (mod q) 
        s2*k - r2*x ≡ M2 (mod q)

在上面得到的二元一次模线性方程组中，系数为s1、s2、r1、r2，未知数为k和x，常数项为M1、M2,模数为q。(m1, r1, s1)和(m2, r2, s2)都是已知，因此可通过求解方程得到私钥x。

例如参开资料<sup>[3]</sup>中的一个实例：

    p = 0x8c286991e30fd5341b7832ce9fe869c0a73cf79303c2959ab677d980237abf7ecf853015c9a086c4330252043525a4fa60c64397421caa290225d6bc6ec6b122cd1da4bba1b13f51daca8b210156a28a0c3dbf17a7826f738fdfa87b22d7df990908c13dbd0a1709bbbab5f816ddba6c8166ef5696414538f6780fdce987552b
    g = 0x49874582cd9af51d6f554c8fae68588c383272c357878d7f4079c6edcda3bcbf1f2cbada3f7d541a5b1ae7f046199f8f51d72db60a2601bd3375a3b48d7a3c9a0c0e4e8a0680f7fb98a8610f042e10340d2453d3c811088e48c5d6dd834eaa5509daeb430bcd9de8aabc239d698a655004e3f0a2ee456ffe9331c5f32c66f90d
    q = 0x843437e860962d85d17d6ee4dd2c43bc4aec07a5

    已知，使用私钥x、固定值k，对m1进行的签名为：
        m1 = 0x3132333435363738
        r1 = 0x4d91a491d95e4eef4196a583cd282ca0e625f36d
        s1 = 0x3639b47678abf7545397fc9a1af108537fd1dfac
    已知，使用私钥x、固定值k，对m2进行的签名为：
        m2 = 0x49276c6c206265206261636b2e
        r2 = 0x4d91a491d95e4eef4196a583cd282ca0e625f36d
        s2 = 0x314c044409a94f4961340212b42ade005fb27b0a

由两次签名结果，得到的二元模线性方程组，其增广矩阵为：

    M1 = int(hashlib.sha1('3132333435363738'.decode('hex')).hexdigest(), 16)
    M2 = int(hashlib.sha1('49276c6c206265206261636b2e'.decode('hex')).hexdigest(), 16)

    matrix = [
        [0x3639b47678abf7545397fc9a1af108537fd1dfac, -0x4d91a491d95e4eef4196a583cd282ca0e625f36d, M1],
        [0x314c044409a94f4961340212b42ade005fb27b0a, -0x4d91a491d95e4eef4196a583cd282ca0e625f36d, M2]
    ] (mod 0x843437e860962d85d17d6ee4dd2c43bc4aec07a5)

求解该方程得：

    k=0x8177f48fcb32d7a6d738210ea9ae63168326a5d6
    x=0x1b4f7f1b231596da9dd3ef03bfe8fb42324d581f

容易验证上面的x即为签名时使用的私钥，k为签名时使用的随机数。


##5 实现代码
本文的实现代码：

https://github.com/55-AA/mod_equations

##6 参考资料
1. 周利敏，单/多模数线性同余方程组的数值解法及其在密码学中的应用
2. 朱萍，初等数论及其在信息科学中的应用，P42，清华大学出版社
3. DSA相关的趣味数学题，http://scz.617.cn/misc/201607011637.txt