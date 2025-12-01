---
title: CSAPPLAB-DATALAB
date: 2025-12-01 8:00:00 +0800
categories: [Computer Basic, CSAPPP]
tags: [Integer, Float]

pin: false
author: 
    name: CALL1CE
    link: https://space.bilibili.com/9330604
toc: true
comments: true
math: true
mermaid: true

---

## 1

```c
//1
/* 
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
  return ~(x & y) & (~((~x)&(~y)));
}
```

​	这道题目，只允许使用~和&来实现异或操作，首先异或就是两位不一样的时候，结果是1，也就是一个是0一个是1。

​	我先是遍历了所有可能的情况：

* x&y：两位同时是1就是1，找到了两位同时是1的位
* ~x&y：y是1时，x是0结果是1，找到了y是1时，xy不同的位
* x&~y：x是1时，x是0结果是1，找到了y是1时，xy不同的位
* ~x&~y：xy同时是0结果就是1，找到了两位同时是0的位

​	其实这个时候，我发现用x&y和~x&~y可以找到两位同时是0或者1的位，那么除了这些情况，不就是二者不同的情况吗？

## 2

```C
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {

  return 1<<31;

}
```

​	这个直接左移即可。

## 3

```c
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
  int tmin = ~x;
  return !((1+x)^(tmin))&!!(x^(~0));
}
//优化
int isTmax(int x) {
    int y = x + 1;
    return !( (y ^ ~x) ) & !!y;
}
```

​	这道题目是利用tmax的性质：

* tmax+1=tmin
* ~tmax = tmin

​	但是还有一个值符合上面的性质，就是-1，那么直接排除掉-1即可

​	优化：我之前是用^直接对比-1这个值，但是实际上可以利用-1+1=0这个性质，这个写法会省两个操作

## 4

```c
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
int allOddBits(int x) {
  int a = 0xAA;
	int b = a << 2;
	int c = b | a;
	int d = c << 4;
	int e = d | c;
	int f = e << 8;
	int g = f | e;
	int h = g << 16;
	int i = h | g;
	int j = x & i;
	int k = j ^ i;
  return !k;
}
//优化
int allOddBits(int x) {
    int mask = 0xAA;              // 00000000 00000000 00000000 10101010
    mask = mask | (mask << 8);    // 00000000 00000000 10101010 10101010  -> 0x0000AAAA
    mask = mask | (mask << 16);   // 10101010 10101010 10101010 10101010  -> 0xAAAAAAAA
    return !((~x) & mask);
}

```

​	这道题就是判断所有奇数位是不是1，如果是，那么返回1，不是就返回0。

​	那么问题来了，怎么判断全部奇数位是不是1呢？

* 只要让x和全部奇数位为1的数也就是0xAAAAAAAA进行一个&，如果x的全部奇数位是1的话（不管偶数位是不是1），得到的答案就是0xAAAAAAAA，否则答案一定不是0xAAAAAAAA。

​	那么怎么返回0或者1呢？

* 根据第3题的写法，使用^就能判断x&0xAAAAAAAA是否是完全一样的，完全一样返回0，这时候取个！就可以了

​	那么允许使用的的int范围在0-255，怎么获得0xAAAAAAAA呢？
* 就不断左移和\|就可以了

​	优化：

​	我之前貌似对0xaa有点错误的理解......实际上只需要两次就可以做出来。

​	对x取~后，如果奇数位所有都是1，那么最后(~x) & mask就是0，反之，但凡有一位奇数位是0，那么(~x) & mask就会存在1。

## 5

```c
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  return ~x+1;
}
```

​	算负数，直接取反加1秒杀了。

## 6

```C
//3
/* 
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x) {
  return (!(x^0x30)|!(x^0x31)|!(x^0x32)|!(x^0x33)|!(x^0x34)|!(x^0x35)|!(x^0x36)|!(x^0x37)|!(x^0x38)|!(x^0x39));
}
```

​	我先想到了暴力做法，通过^可以遍历一下x是不是‘0’-‘9’，是那么必然有一项是0其他都是大于0的数，取个！就能把这个关系变成1和0，再一\|，但凡有一个1就是1，也就是说在‘0‘-‘9’。但是这不符合条件要求的15个ops。

```C
int isAsciiDigit(int x) {
  return (!((x+(~0x30+1)>>31) | ((0x39+(~x+1))>>31)));
}
```

​	我又想到一个办法，就是让通过x-0x30>=0和0x39>=0同时满足就可以，之前我一直以为！是可以直接判断正负的，我以为大于0的取！会得到0，小于等于0的取！会得到1，然而事实是，0取！得到1，非0取！得到0。

* 通过x>>31&1可以判断正负，但是这里不需要&1，因为最后会有！兜。

```c
int isAsciiDigit(int x) {
    int lower = x + (~0x30 + 1);  // x - 0x30
    int upper = 0x39 + (~x + 1);  // 0x39 - x
    int sign  = (lower | upper) >> 31; // 只要有一个为负，最高位就是1（-1）

    return !sign;  // sign=0 → 在区间内 → 返回1；sign=-1 → 返回0
}

```

​	这个方式可以少一个op。

## 7

```C
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
  int a = ~(!x)+1;
  return (y & (~a)) | (z & a);
}
```

​	我首先想要实现的一点是y oprator expression = 全0或者本身，z同理，这样的话二者取|就可以得到y或者z了。

​	那么我又开始遍历了

* y & 0 = 0
* y & 全1 = y（全1其实也就是-1）
* y \| -1 = -1
* y \| 0 = y

​	那么现在问题变成了怎么根据x的变换来获得0和-1，那么已知通过！可以根据x是否非0得到0或1，那么再取个负数，0还是0，1就变成全1了也就是-1，颗秒！邦邦邦邦！

## 8

```C
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
  int isOppositeSign = (x^y)>>31&1;
  int isxNeg = x>>31&1; 
  int sameSign = (y + (~x + 1))>>31&1;
  //conditional(isOppositeSign, isxNeg, !sameSign)
  int a = ~(!isOppositeSign)+1;
  return (isxNeg & (~a)) | (!sameSign & a);
}
//优化：
int isLessOrEqual(int x, int y) {
    int sx = (x >> 31) & 1;           // x 的符号：x<0 → 1, 否则 0
    int sy = (y >> 31) & 1;           // y 的符号
    int signDiff = sx ^ sy;           // 符号是否不同：1 表示异号

    int diff = y + (~x + 1);          // y - x
    int sd = (diff >> 31) & 1;        // y-x 的符号：<0 → 1, 否则 0

    // 情况1：符号不同时，只要 x<0,y>=0 就一定 x<=y
    //   signDiff=1 且 sx=1 → x<=y
    // 情况2：符号相同，则看 y-x 是否非负（sd=0）
    //   signDiff=0 且 sd=0 → x<=y
    return (signDiff & sx) | ((!signDiff) & (!sd));
}

```

​	这道题直接复用上道题目的答案了。这道题其实本意还是判断正负，x<=y挪过去，y-x>=0也就是判断y-x的符号是否是正数即可，也就是说，右移31位&1后，如果结果是0，那么这个不等式就成立了，这也是为什么没有把y往左挪的原因。如果是x-y<=0，那么同样的方式右移31位&1后，如果结果是1，只能说明x-y<0，等于不在里面。

​	好了，现在要解决第二个问题，也就是溢出问题，那么现在开始考虑，什么时候会溢出？

* xy同号的时候，肯定不会溢出，直接用上面的公式判断不等式结果即可
* xy异号的时候，就可能会溢出了，那么异号又有两种情况
  * x正y负，上面的不等式肯定不成立，也就是返回0
  * x负y正，上面的不等式肯定成立，也就是返回1

​	那么现在问题变成了，先判断xy是同号还是异号，如果是同号，那么就走不等式判断正负的分支。如果xy是异号，那么就根据x是否是负数，来返回1或者0即可。

​	优化：其实类似conditionnal的判断，这样子省一些ops。

## 9

```C
//4
/* 
 * logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int logicalNeg(int x) {
  int y = ~x+1;
  return (((x>>31&(~0)))|(y>>31&(~0)))+1;
}
//优化：
int logicalNeg(int x) {
  int y = ~x + 1;
  return ((x >> 31) | (y >> 31)) + 1;
}
//优化：
int logicalNeg(int x) {
    return ((x | (~x + 1)) >> 31) + 1;
}
```

​	这道题要利用0的特性，也就是0的负数还是0，但是正数和负数时相反数，但但是，tmin的负数还是本身，奇妙的是，如果用>>31&1的方式来判断正负的话，对于0而言就永远是0，所以对0取正负然后用>>31&1判断就只有一种情况也就是00（指的是正数符号是0，负数符号也是0），对于非0值x，当对x取负数的时候，x>>31&1有三种情况，就是11（如tmin正数负数都是本身，都是负数）、10、01，所以这样就把0和非0区分开了。

​	还有一点就是0要返回1，非0要返回0，这一点比较反人类，不能>>31&1得到x的正负数后直接用\|，因为这样的结果是相反的，所以此处得用个小巧思，让判断正负的式子&-1，这样的话，如果就变成了-1-1、-10、0-1、00，然后\|之后加一就行了。

​	优化：补码中，正负数右移就是`0x00000000` 或 `0xFFFFFFFF`，不需要&全1了。

​	优化：其实真正需要的就只有符号位罢了，所以先正负数\|了，再取符号也可以，省ops。

## 10

```C
/* howManyBits - return the minimum number of bits required to represent x in
 *             two's complement
 *  Examples: howManyBits(12) = 5
 *            howManyBits(298) = 10
 *            howManyBits(-5) = 4
 *            howManyBits(0)  = 1
 *            howManyBits(-1) = 1
 *            howManyBits(0x80000000) = 32
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 90
 *  Rating: 4
 */
int howManyBits(int x) {
  int a = conditional(x>>31&1,~x,x);
	int pos = 0;
	int bits16, bits8, bits4, bits2, bits1,bits0;
	bits16 = !!(a >> 16) << 4;
	a = a >> bits16;
	bits8 = !!(a >> 8) << 3;
	a = a >> bits8;
	bits4 = !!(a >> 4) << 2;
	a = a >> bits4;
	bits2 = !!(a >> 2) << 1;
	a = a >> bits2;
	bits1 = !!(a >> 1);
	a = a >> bits1;
  bits0 = !!(a);
	pos = bits16 + bits8 + bits4 + bits2 + bits1 + bits0;
	return pos+1;
}
//优化：
int howManyBits(int x) {
    int sign = x >> 31;          // 0 or -1
    int a = x ^ sign;            // x>=0 -> a=x; x<0 -> a=~x

    int bits16, bits8, bits4, bits2, bits1, bits0;
    int pos;

    bits16 = !!(a >> 16) << 4;
    a >>= bits16;

    bits8 = !!(a >> 8) << 3;
    a >>= bits8;

    bits4 = !!(a >> 4) << 2;
    a >>= bits4;

    bits2 = !!(a >> 2) << 1;
    a >>= bits2;

    bits1 = !!(a >> 1);
    a >>= bits1;

    bits0 = a;                  // 此时 a 是 0 或 1

    pos = bits16 + bits8 + bits4 + bits2 + bits1 + bits0;
    return pos + 1;
}

```

​	这道题目首先要理解一个定义，也就是**有效位**。对于正数而言，有效位就是1所在最高位，因为最高位的1左侧的0只是拓展符号位，如000110这个二进制数的前3个0，相当于一个0的作用，也就是符号位，和0110值是一样的。对于负数而言，恰恰相反，有效位就是0所在的最高位，因为最高位0左侧的1相当于拓展符号位，如11101，左边3个1相当于一个1的作用，也就是和101值是一样的。

​	正数还好理解，负数怎么理解呢？其实可以这么想，对于11101来说，相当于
$$
-2^4+2^3+2^2+2^0
$$
​	而前三项加起来，就是$$ -2^2 $$，这样的话，不就相当于101了吗，所以前置1都是拓展符号位。

​	所以对于负数，最开始取个~就能和正数一样计算了，算1所在下标，然后+1即可。

​	然后要解决的问题就是怎么算出来最高位的下标，用二分法即可。先判断是在高16位还是低16位，如果在高16位，那么就把这个数右移16位，否则就不用管。然后在去判断是在当前这个数的高8位还是低8位，以此类推，就能得到最高位1所在的下标。

​	对于0和-1这两个数来说，符号位也可以作为数字位，可以想象1这个二进制数，作为补码，也就是$$-2^0 $$，不就是-1吗。

​	优化：用^全0或者全1，就可以得到本身或者取~。

## 11

```C
//float
/* 
 * floatScale2 - Return bit-level equivalent of expression 2*f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representation of
 *   single-precision floating point values.
 *   When argument is NaN, return argument
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
unsigned floatScale2(unsigned uf) {
	unsigned s = uf >> 31 & 1;
	unsigned maskSExp = (~0 << 23);
	unsigned exp = (uf & maskSExp) << 1 >> 24;
	unsigned maskM = ~maskSExp;
	unsigned m1 = uf & maskM;
	unsigned newExp;
	unsigned newM1;
	if (!(exp | 0)) {
		newExp = m1 >> 22 & 1;
		newM1 = m1 << 1;
	}
	else {
		newExp = exp + !!(exp ^ 0xff);
		newM1 = m1;
	}

	return (s << 31) | (newExp << 23) | (newM1 & maskM);
}
//优化：
unsigned floatScale2(unsigned uf) {
    unsigned s   = uf & 0x80000000u;    // 符号位
    unsigned exp = (uf >> 23) & 0xFF;   // 指数字段 [0..255]
    unsigned frac = uf & 0x7FFFFFu;     // 尾数字段

    // 情况 1：exp == 255，NaN 或 Inf，按题意原样返回
    if (exp == 0xFF) {
        return uf;
    }

    // 情况 2：exp == 0，0 或 非规格化数
    if (exp == 0) {
        // 非规格化：倍乘就是 frac 左移一位
        frac <<= 1;
        // 如果左移后“顶到了隐藏位”，变成规格化数
        if (frac & 0x800000u) {      // 检查 bit23
            exp = 1;
            frac &= 0x7FFFFFu;      // 清掉 bit23，只保留 22:0
        }
    } else {
        // 情况 3：规格化有限数：指数 +1
        exp += 1;
        // 如果加到 255，说明溢出到 +Inf，尾数清零
        if (exp == 0xFF) {
            frac = 0;
        }
    }

    return s | (exp << 23) | frac;
}

```

​	这道题首先要明白浮点数的定义和乘法和特殊条件。

​	首先，浮点数由三部分构成，S、Exp、M。其中S一位，表示符号位，Exp八位是阶码，M二十三位是尾数。

​	其次E=Exp-Bias，$$Bias=2^k-1$$，对于8位，就是127。

​	一个浮点数计算结果就是：$$ (−1)^S*M*2^E $$。

​	所以乘以2还是很容易的，一般情况下就直接让exp+1即可，这里需要考虑几个特殊情况，主要就是非规格化数部分。

​	第一，当exp全1的时候，那么乘以2，此时不需要+1，全1要么是正无穷或者负无穷，要么就是Nan。

​	第二，当exp全0的时候，那么乘以2，就不一定是+1了，此时还有两个情况，如果尾数没有前置1，那么只是把尾数左移即可，exp不用变。如果有前置1，仍然需要把尾数左移，只是这时候，从非规格化数到了规格化数，所以exp就要+1了，记得尾数左移要截断那个1，因为此时变成了潜在1。

​	优化：

​	首先，exp的算法可以直接用uf右移去算；其次，我疏忽了一个情况，就是exp=254，frac不为0，此时乘以2应该是inf，而不是Nan

## 12

```C
/* 
 * floatFloat2Int - Return bit-level equivalent of expression (int) f
 *   for floating point argument f.
 *   Argument is passed as unsigned int, but
 *   it is to be interpreted as the bit-level representation of a
 *   single-precision floating point value.
 *   Anything out of range (including NaN and infinity) should return
 *   0x80000000u.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
int floatFloat2Int(unsigned uf) {
  unsigned s = uf >> 31;
  unsigned exp = (uf>>23) & 0xff;
  unsigned frac = uf&0x7fffff;
  unsigned m = (1u<<23)|frac;
  unsigned ans;
  int E = exp - 127;
  
  if(E <= -1) return 0;
  if(E > 30) return 0x80000000u;
  if(E>=23){
    ans = m<<(E-23);
  }else{
    ans = m>>(23-E); 
  }
  if(s) return ~ans+1;
  
  return ans;
}
```

​	规格化数：E=exp-Bias(127)，非规格化数E=1-Bias，这道题完全不用考虑非规格化数，所以直接算规格化数即可，尾数是有个潜在1的，把这个注意好就可以。

## 13

```C
/* 
 * floatPower2 - Return bit-level equivalent of the expression 2.0^x
 *   (2.0 raised to the power x) for any 32-bit integer x.
 *
 *   The unsigned value that is returned should have the identical bit
 *   representation as the single-precision floating-point number 2.0^x.
 *   If the result is too small to be represented as a denorm, return
 *   0. If too large, return +INF.
 * 
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. Also if, while 
 *   Max ops: 30 
 *   Rating: 4
 */
unsigned floatPower2(int x) {
    if(x >= 128) return 0x7f800000;
    if(x < -149) return 0;
    if(x<=-127) return 1u << (x+149);

    return (x + 127)<<23;
}
```

​	这道题目主要就是考虑几个情况。

​	第一，x>=128也就是当exp全1的时候，E=exp-Bias=255-127=128的时候就已经需要返回inf了，也就是正无穷。

​	第二，当x<-149也就是当exp全0的时候，E=1-Bias=1-127=-126，然后尾数是1，-126-23=-149，此时是能取到的最小值，那么小于-149的，就返回0。

​	第三，当介于[-127,-149]之间的，都属于非规格化数，exp还是全0，尾数不停左移即可。

​	第三就是其他范围都属于规格化数，算出来exp即可，尾数就是全0。

## Results

第一次全通过时的分数：

Correctness Results     Perf Results

Points  Rating  Errors  Points  Ops     Puzzle

1       1       0       2       7       bitXor

1       1       0       2       1       tmin

1       1       0       2       9       isTmax

2       2       0       2       11      allOddBits

2       2       0       2       2       negate

3       3       0       2       12      isAsciiDigit

3       3       0       2       7       conditional

3       3       0       2       18      isLessOrEqual

4       4       0       2       10      logicalNeg

4       4       0       2       41      howManyBits

4       4       0       2       23      floatScale2

4       4       0       2       17      floatFloat2Int

4       4       0       2       9       floatPower2

Score = 62/62 [36/36 Corr + 26/26 Perf] (167 total operators)
