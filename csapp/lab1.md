## 代码

```c
// 1
/*
 * bitXor - x^y using only ~ and &
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) { return ~(~(~x & y) & ~(x & ~y)); }
/*
 * tmin - return minimum two's complement integer
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) { return 1 << 31; }
// 2
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
  int tmin = x + 1;

  return !(((x + tmin) ^ ~0) | !(x ^ ~0));
}
/*
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most
 * significant) Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) =
 * 1 Legal ops: ! ~ & ^ | + << >> Max ops: 12 Rating: 2
 */
int allOddBits(int x) {
  int a = 0xAA;
  a |= 0xAA << 8;
  a |= 0xAA << 16;
  a |= 0xAA << 24;
  x &= a;
  return !(x ^ a);
}
/*
 * negate - return -x
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) { return ~x + 1; }
// 3
/*
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters
 * '0' to '9') Example: isAsciiDigit(0x35) = 1. isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x) {
  return !((x + (~0x30 + 1)) & (1 << 31)) & !((0x39 + (~x + 1) & (1 << 31)));
}

/*
 * conditional - same as x ? y : z
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
  // !x的取值是0和1, 而我们需要的是全0和全1
  // !x - 1恰好可以将值域迁移到全0和全1
  // x非0时, !x - 1全1
  int mask = !x + ~0;

  // 之后只要用mask去和y z做&, 全1去&保留, 全0去&清空
  return (mask & y) | (~mask & z);
}
/*
 * isLessOrEqual - if x <= y  then return 1, else return 0
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
  int mask = (1 << 31);
  int xsign = x & mask;
  int ysign = y & mask;

  int dif = ((xsign ^ ysign) >> 31) & 1;

  int t = x + (~y + 1);
  return (dif & (xsign >> 31)) | (!dif & (!t | !!(t & (1 << 31))));
}
// 4
/*
 * logicalNeg - implement the ! operator, using all of
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4
 */
int logicalNeg(int x) { return ((x | (~x + 1)) >> 31) + 1; }

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
  // 借鉴别人, 真的学到了, 五体投地了
  int mask = x >> 31;
  int need_low_16, need_low_8, need_low_4, need_low_2, need_low_1;
  int mask16, mask8, mask4, mask2, mask1;

  // 先看高16位纯不纯
  mask16 = (0xFF << 24) | (0xFF << 16);
  need_low_16 = !!((x & mask16) ^ (mask & mask16));
  x >>= need_low_16 << 4;

  mask8 = 0xFF << 8;
  need_low_8 = !!((x & mask8) ^ (mask & mask8));
  x >>= need_low_8 << 3;

  mask4 = 0xF << 4;
  need_low_4 = !!((x & mask4) ^ (mask & mask4));
  x >>= need_low_4 << 2;

  mask2 = 0x3 << 2;
  need_low_2 = !!((x & mask2) ^ (mask & mask2));
  x >>= need_low_2 << 1;

  mask1 = 0x1 << 1;
  need_low_1 = !!((x & mask1) ^ (mask & mask1));
  x >>= need_low_1;

  return (need_low_16 << 4) + (need_low_8 << 3) + (need_low_4 << 2) +
         (need_low_2 << 1) + need_low_1 + 1 + !(x & 1 & (mask & 1)) + !!x + ~0;
}
// float
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

  int exp = (uf & (0xFF << 23)) >> 23;

  // 特判inf, nan, +0, -0
  if (!(exp ^ 0xFF) || !(uf & ~(1 << 31))) {
    return uf;
  }

  // 特判exp == 0
  if (!exp) {
    // 规格化和非规格化的值可以平滑转移, 无需特判, 太酷了
    int sign = (uf >> 31) & 1;
    uf <<= 1;
    uf |= (sign << 31);
    return uf;
  }

  exp += 1;
  uf = (uf & ~(0xFF << 23)) | (exp << 23);

  return uf;
}

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
  const int bias = 127;
  int sign = uf >> 31;

  int t = (0xFF << 23);
  int exp = ((uf & t) >> 23) & 0xFF;
  int frac = (uf & ~(t | (1 << 31)));
  int M = 0, E = 0, abs = 0;

  // 特判inf, nan
  if (!(exp ^ 0xFF)) {
    return 1 << 31;
  }

  // 特判exp == 0
  if (!exp) {
    E = 1 - bias;
    M = frac;
  } else {
    E = exp - bias;
    M = frac | (1 << 23);
  }

  if (E - 23 < 0) {
    int abs = 23 - E;
    if (abs >= 32) {
      return 0;
    }
    t = M >> abs;
    if (sign)
      return ~t + 1;
    return t;
  }

  abs = E - 23;
  if (abs >= 32)
    return 1 << 31;

  t = M >> abs;
  if (sign)
    return ~t + 1;
  return t;
}

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
  const int bias = 127;

  // [-126, 127]
  if (x < -126) {
    return 0;
  }
  if (x > 127) {
    return 0xFF << 23;
  }

  return (x + bias) << 23;
}

```
