# [산술 연산자(Arithmetic operators)](#arithmetic-operators)

* Go 버전: 1.9
* 원문: [Arithmetic Operators](https://golang.org/ref/spec#Arithmetic_operators)
* 번역자: Jhonghee Park (@jhonghee)

Arithmetic operators apply to numeric values and yield a result of the same type as the first operand. The four standard arithmetic operators (`+`, `-`, `*`, `/`) apply to integer, floating-point, and complex types; `+` also applies to strings. The bitwise logical and shift operators apply to integers only.

산술 연산자는 숫자 값들에 적용되어 첫번째 피연산자와 같은 타입의 결괏값을 생산한다. 4가지 표준 산술 연산자들(`+`, `-`, `*`, `/`)은 정수타입, 부동 소수점 타입, 그리고 복소수 타입에 적용되고; `+`는 string 타입에 또한 적용된다. 비트 논리 연산자와 시프트 연산자들은 정수들에만 적용된다.

```
+    sum                    integers, floats, complex values, strings
-    difference             integers, floats, complex values
*    product                integers, floats, complex values
/    quotient               integers, floats, complex values
%    remainder              integers

&    bitwise AND            integers
|    bitwise OR             integers
^    bitwise XOR            integers
&^   bit clear (AND NOT)    integers

<<   left shift             integer << unsigned integer
>>   right shift            integer >> unsigned integer
```

```
+    sum                    정수, 부동 소수, 복소수 값, 문자열
-    difference             정수, 부동 소수, 복소수 값
*    product                정수, 부동 소수, 복소수 값
/    quotient               정수, 부동 소수, 복소수 값
%    remainder              정수

&    bitwise AND            정수
|    bitwise OR             정수
^    bitwise XOR            정수
&^   bit clear (AND NOT)    정수

<<   left shift             정수 << 부호없는 정수
>>   right shift            정수 >> 부호없는 정수
```

## [정수 연산자들(Integer operators)](#integer-operators)

For two integer values `x` and `y`, the integer quotient `q = x / y` and remainder `r = x % y` satisfy the following relationships:

2개의 정수값 `x`와 `y`에 대해, 몫인 `q = x / y`와 나머지인 `r = x % y`는 다음과 같은 관계를 만족시킨다:

```
x = q*y + r  and  |r| < |y|
```

```
x = q*y + r  그리고  |r| < |y|
```

with `x / y` truncated towards zero ("truncated division").

`x / y`는 0값을 겨냥해서 소수점이하를 절단하는 방식이다("절단식 나누기(truncated division)").

```
 x     y     x / y     x % y
 5     3       1         2
-5     3      -1        -2
 5    -3      -1         2
-5    -3       1        -2
```

As an exception to this rule, if the dividend `x` is the most negative value for the int type of `x`, the quotient `q = x / -1` is equal to `x` (and `r = 0`).

이 규칙의 한 예외로, 만약 피제수 `x`가 `x`에 주어진 int 타입의 가장 작은 음수 값이면, 몫인 `q = x / -1`는 `x`와 같다. (그리고 `r = 0`)

```
			 x, q
int8                     -128
int16                  -32768
int32             -2147483648
int64    -9223372036854775808
```

If the divisor is a constant, it must not be zero. If the divisor is zero at run time, a run-time panic occurs. If the dividend is non-negative and the divisor is a constant power of 2, the division may be replaced by a right shift, and computing the remainder may be replaced by a bitwise AND operation:

만약 제수가 상수라면, 절대로 0일 수 없다. 런타임에 0으로 나누기를 하면, 런타임 panic이 일어난다. 만약 피제수가 음수가 아니고 제수가 2의 거듭제곱인 상수라면, 이 나누기는 우향 시프트(right shift)로 대체될 수 있으며, 나머지 계산은 비트 AND 연산으로 대체할 수 있다:

```
 x     x / 4     x % 4     x >> 2     x & 3
 11      2         3         2          3
-11     -2        -3        -3          1
```

The shift operators shift the left operand by the shift count specified by the right operand. They implement arithmetic shifts if the left operand is a signed integer and logical shifts if it is an unsigned integer. There is no upper limit on the shift count. Shifts behave as if the left operand is shifted `n` times by 1 for a shift count of `n`. As a result, `x << 1` is the same as `x*2` and `x >> 1` is the same as `x/2` but truncated towards negative infinity.

시프트 연산자들은 좌측 피연산자를 우측 피연산자가 지정하는 값만큼 시프트한다. 좌측 피연산자가 부호있는 정수인 경우 산술적으로 시프트를 구현하고 부호없는 정수인 경우는 논리적인 시프트를 구현한다. 시프트하는 수의 최대값에 대한 제한은 없다. 횟수가 `n`인 시프트 연산은 왼쪽 피연산자를 1번 시프트하는 연산 결과을 `n`번 반복하는 것 처럼 동작한다. 결과적으로 `x << 1`은 `x*2`와 같고 `x >> 1`은 `x/2`를 계산한 다음 무한히 작은 음수를 향해 소수점이하를 절단하는 것과 같다.

For integer operands, the unary operators `+`, `-`, and `^` are defined as follows:

정수 연산자들에 대해서, 단항 연산자 `+`, `-`, 그리고 `^`는 다음과 같이 정의된다:

```
+x                          is 0 + x
-x    negation              is 0 - x
^x    bitwise complement    is m ^ x  with m = "all bits set to 1" for unsigned x
                                      and  m = -1 for signed x
```

```
+x는 0 + x 이다
-x는 음수화로 0 - x 이다
^x는 비트 보수 연산인 m ^ x로서 부호없는 x에 대해 m의 "모든 비트가 1로 주어진다"
                           그리고 부호있는 x에 대해 m은 -1로 주어진다.
```

## [정수 오버플로우(Integer overflow)](#integer-overflow)

For unsigned integer values, the operations `+`, `-`, `*`, and `<<` are computed modulo 2<sup>n</sup>, where `n` is the bit width of the [unsigned integer](/Types/numeric_types.html)'s type. Loosely speaking, these unsigned integer operations discard high bits upon overflow, and programs may rely on "wrap around".

부호없는 정수값에 대해, `+`, `-`, `*`, 그리고 `<<` 연산들은 2<sup>n</sup> 모듈로(modulo)에 의한 계산값으로, `n`은 [부호없는 정수](/Types/numeric_types.html) 타입의 비트 너비이다. 대략적으로 얘기하자면, 이 부호없는 정수의 연산들은 오버플로우가 발생할 때 높은 위치의 비트들을 버리고, 프로그램들은 "값 두루기(wrap around)"에 의존할 수도 있다.

For signed integers, the operations `+`, `-`, `*`, and `<<` may legally overflow and the resulting value exists and is deterministically defined by the signed integer representation, the operation, and its operands. No exception is raised as a result of overflow. A compiler may not optimize code under the assumption that overflow does not occur. For instance, it may not assume that `x < x + 1` is always true.

부호있는 정수에 대해, `+`, `-`, `*`, 그리고 `<<` 연산들은 오버플로우가 허용되며 결괏값이 존재한다. 이 연산들은 부호있는 정수의 표시법, 연산, 그리고 피연산자에 의해 결정론적으로 정의된다. 오버플로우의 결과에 대해 아무런 예외적인 처리를 하지 않는다. 컴파일러는 오버플로우가 발생하지 않는다는 가정하에 코드를 최적화하지 않을 수도 있다. 예를 들어, `x < x + 1`가 항상 옳다는 가정을 하지 않을 수도 있다.

## [부동 소수점 연산자(Floating-point operators)](#floating-point-operators)

For floating-point and complex numbers, `+x` is the same as `x`, while `-x` is the negation of `x`. The result of a floating-point or complex division by zero is not specified beyond the IEEE-754 standard; whether a [run-time panic](/Run-time%20panics/) occurs is implementation-specific.

부동 소수점 숫자들과 복소수에 대해, `-x`가 `x`의 음수화인 반면에, `+x`는 `x`와 같다. 부동 소수점 수와 복소수를 0으로 나눈 결과에 대한 사양은 IEEE-754 표준을 벗어나지 않는다; [런타임 panic](/Run-time%20panics/)을 일으킬 지는 구현에 따라 다를 수 있다.

## [문자열 연결(String concatenation)](#string-concatenation)

Strings can be concatenated using the `+` operator or the `+=` assignment operator:

문자열들은 `+` 연산자나 `+=` 할당 연산자를 사용해 연결될 수 있다:

```
s := "hi" + string(c)
s += " and good bye"
```

String addition creates a new string by concatenating the operands.

문자열 더하기는 피연산자들을 연결함으로써 새로운 문자열을 만든다.