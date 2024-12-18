---
title: "C 语言位操作技巧"
date: 2022-07-03T09:44:13+08:00
tags: [c]
categories: ["C Language"]
description: Some hacks about bit-operation. Updating...

---

## 连续内存取n bit
```c
#include <stdio.h>
#include <stdint.h>
#include <assert.h>

#define bitmask(n) ((1ul << (n)) - 1)

/* 
 * 从ptr指向的内存开始，抽取第start个bit开始的连续n个bit
 * 限制: n < 32
 */
uint32_t extract_bits(uint8_t *ptr, uint32_t start, uint32_t n)
{
  uint32_t start_byte = start / 8;
  uint32_t start_offset = start % 8;
  uint32_t *pstart = (uint32_t *)(ptr + start_byte);

  uint32_t end = start + n - 1;
  uint32_t end_byte = end / 8;
  uint32_t end_offset = end % 8;
  uint32_t *pend = (uint32_t *)(ptr + end_byte);

  uint32_t data = *pstart >> start_offset;

  if (n > 32 - start_offset) {
    /* 由于n < 32, 所以补齐*pend一定就够了，
     * end_offset对齐到最后一位(n-1).
     * 
     * 严谨性证明: n 一定> end_offset + 1
     * 因为n > 32-start_offset ==> n > 25, 
     * 且end_offset + 1 < 9, 故得证
     */
    data |= *pend << (n - end_offset - 1);
  }

  return data & bitmask(n);
}

void test_val(uint32_t val, uint32_t expect)
{
  if (val != expect) {
    printf("error: val: 0x%x, expect: 0x%x\n", val, expect);
  }
  assert(val == expect);
}


int main(void)
{
  uint32_t vals[] = {0x11223344, 0x11223344};
  uint32_t ret;

  ret = extract_bits((uint8_t *)vals, 31, 30);
  test_val(ret, 0x22446688);


  printf("Test Passed!\n");
  return 0;
}
```

## 一个数取连续n bit
```c
/*
 * 从一个数中取第start个bit开始的连续n个bit
 */
uint32_t extract_bits (uint32_t val, uint32_t start, uint32_t n)
{
  return (val >> start) & bitmask(n);
}
```

## 判断一个数是否为2的幂
```c
unsigned int v;

if ((v & (v - 1)) == 0) 
    printf("v is a power of 2\n");
else 
    printf("v is not a power of 2\n");
```

&nbsp;
## 统计一个数的二进制中1的数量

依然是利用`v & (v -1)`的运算结果会将v的最低位的`1`(如果有的话)置`0`.

循环执行此操作就可统计v中`1`的数量.

```c
int numberof1(int v) {
    int count = 0;

    while(v) {
        count++;
        v = v & (v -1);
    }
    return count;
}
```


&nbsp;
## 将一个数向上取整为2的幂

用一个`1`一直左移, 直到比这个数大为止.

```c
uint32_t roundup_pow_of_two(const uint32_t x) {
    uint32_t ret = 1;

    while (ret < x) {
        ret = ret << 1;
    }
    return ret;
}
```

Linux内核中使用了一种更快的方案, amazing!!!

```c
static __inline__ int generic_fls(int x)
{
	int r = 32;

	if (!x)
		return 0;
	if (!(x & 0xffff0000u)) { 
		x <<= 16;
		r -= 16;
	}
	if (!(x & 0xff000000u)) {
		x <<= 8;
		r -= 8;
	}
	if (!(x & 0xf0000000u)) {
		x <<= 4;
		r -= 4;
	}
	if (!(x & 0xc0000000u)) {
		x <<= 2;
		r -= 2;
	}
	if (!(x & 0x80000000u)) {
		x <<= 1;
		r -= 1;1
	}
	return r;
}

static inline unsigned long __attribute_const__ roundup_pow_of_two(unsigned long x)
{
	return (1UL << generic_fls(x - 1));
}
```



&nbsp;

## 向上/向下对齐, 检查是否对齐
```c
/* uintptr_t 代表指针的位数
 * 加uintptr_t转换的原因是: (void *)不能进行运算
 */
#define IS_ALIGNED(X, align)  (((uintptr_t)(const void *)(X)) % (align) == 0)
#define ALIGN_UP(X, align)   (((X) + ((align) - 1)) & ~((align) - 1))
#define ALIGN_DOWN(x, align) ((X) & ~((align) - 1))

#define X      (0x12345675)
#define align  (1 << 2)

int main()
{
    int v = IS_ALIGNED(X, align);

    if (0 == v) {
        printf("Given X(0x%x) is not align to 0x%08x\n", X, align);
        printf("After align up,   new X = 0x%x\n", ALIGN_UP(X, align));
        printf("After align down, new X = 0x%x\n", ALIGN_DOWN(X, align));
    } else {
        printf("Give X(0x%x) is aligned to 0x%08x\n", X, align);
        printf("After align up,   new X = 0x%x\n", ALIGN_UP(X, align));
        printf("After align down, new X = 0x%x\n", ALIGN_DOWN(X, align));
    }

    return 0;
}
```


&nbsp;
## 检查两个有符号数是否异号
```c
int x,y;

if ((x ^ y) < 0) 
    printf("They have opposite signs\n");
else
    printf("They have same signs\n");
```

&nbsp;
## 大小端转换


&nbsp;
## 对某个位的get/set/clear操作

```c
#define GET_BIT(x, bit)     ( ((x) & (1ULL << (bit))) >> (bit) )
#define SET_BIT(x, bit)     ( (x) |= (1ULL << (bit)) )
#define CLEAR_BIT(x, bit)   ( (x) &= ~(1ULL << (bit)) )
```
> Release note: 
> 1. 添加对`unsigned long long`长度的支持

&nbsp;
## Sign extending from a varaiable bit-width

```c
    int bits = 2 * 8; // number of bits representing the number in x
    int x = 0xFFC1;   // ready to get sign-extended
    int rst;          // resulting sign-extended number
    int const mask = 1U << (bits - 1); // mask can be  pre-computed if bits if fixed.

    x = x & ((1U << bits) - 1); // cut x if it holds more bits
    rst = (x ^ mask) - mask;    // excellent trick!

    printf("INPUT: 0x%x, RESULT: 0x%x\n", x, rst);
```

&nbsp;
## 字符/字符数组的大小写转换
```c
#define TO_LOWER(c) (unsigned char)((c >= 'A' && c <= 'Z') ? (c | 0x20) : c)
#define TO_UPPER(c) (unsigned char)((c >= 'a' && c <= 'z') ? (c & ~0x20) : c)

#define TO_LOWER_STR(s, len) { \
    for (int i = 0; i < len && s[i] != '\0'; i++) { \
        s[i] = TO_LOWER(s[i]); \
    } \
}

#define TO_UPPER_STR(s, len) {\
    for (int i = 0; i < len && s[i] != '\0'; i++) { \
        s[i] = TO_UPPER(s[i]); \
    } \
}
```
