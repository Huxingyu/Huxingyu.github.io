---
layout: post
title: 赏花归去马如飞--关乎回文
description: 欧拉计划中有一些关于回文的有趣题目，下面对其P4,P36,P55,P125进行解决。
category: blog
---


赏花归去马如飞，去马如飞酒力微。 酒力微醒时已暮，醒时已暮赏花归。

据说是东坡即兴所做的一首小诗，不得不说一气呵成，才华滂沱。回文诗大多为回文而回文，无甚趣处，前人姑且一和，我们后辈们姑且一赏就是了。但东坡的这首实在不错，值得把玩。

这段时间有时刷刷欧拉计划，恰好碰到了许多关于回文数的题目，恰好可以与诗拿来一块说说，拿东坡的诗，做欧拉的题，有些意思。


1(P4),Largest palindrome product
-
```
A palindromic number reads the same both ways. The largest palindrome made from the product of two 2-digit numbers is 9009 = 91 × 99.

Find the largest palindrome made from the product of two 3-digit numbers.
```
这个题目思路很简单，考虑到求最大结果，所以从999×999开始递减就可以，我的想法是利用整除与取余将结果的前三项（倒置）与后三项比较，然后就可以得出结果。但是比较麻烦，最后采取的方法是直接将这个数整个翻转，然后对比原数，用一个双重循环找出最大值就可以了。使用C语言，下面给出代码：
```c
#include <stdio.h>

int is_palindrome( int number ){
        int res = 0;
        int n = number;
        if ( n < 0 ){
            return 0;
        }
        do{
            res = res * 10 + n % 10;
            n = n / 10;
        } while ( n );

        if ( res == number ){
            return 1;
        }
        return 0;
}

int solution(){
    int x, y, v, largest_number;
    largest_number=0;

    for(x = 999; x > 100; x--){
        for(y = 999; y > 100; y--){
            v = x * y;
            if(v > largest_number && is_palindrome(v) == 1){
                largest_number = v;
            }
        }
    }
    return largest_number;
}

int main(){
    return solution();
}
```

2(P36),Double-base palindromes
-
```
The decimal number, 585 = 10010010012 (binary), is palindromic in both bases.

Find the sum of all numbers, less than one million, which are palindromic in base 10 and base 2.

(Please note that the palindromic number, in either base, may not include leading zeros.)
```

这个题目要求小于一百万的在十进制和二进制下均为回文数的数值之和，那么一个循环进行判断，之后连加求和就可以了，主要问题在于十进制到二进制的转换，因为判断程序在P4中就已经给出了，拿来直接用就行。这个采用python语言，下面给出程序：

```python
def huiwen_judge(string):
    if str(string) == str(string)[::-1]:
        return True
    else:
        return 0
def jinzhi_change(n):
    if n==0:
        return '0'
    list = ''
    while n>=1:
        if n%2==0:
            list += '0'
        else:
            list += '1'
        n=int(n/2)
    return list[::-1]
sum=0
for i in range(1,1000000):
    if huiwen_judge(str(i)) and huiwen_judge(jinzhi_change(i)):
        sum+=i
print sum
```

3(P55),Lychrel numbers
-
```
If we take 47, reverse and add, 47 + 74 = 121, which is palindromic.

Not all numbers produce palindromes so quickly. For example,

349 + 943 = 1292,
1292 + 2921 = 4213
4213 + 3124 = 7337

That is, 349 took three iterations to arrive at a palindrome.

Although no one has proved it yet, it is thought that some numbers, like 196, never produce a palindrome. A number that never forms a palindrome through the reverse and add process is called a Lychrel number. Due to the theoretical nature of these numbers, and for the purpose of this problem, we shall assume that a number is Lychrel until proven otherwise. In addition you are given that for every number below ten-thousand, it will either (i) become a palindrome in less than fifty iterations, or, (ii) no one, with all the computing power that exists, has managed so far to map it to a palindrome. In fact, 10677 is the first number to be shown to require over fifty iterations before producing a palindrome: 4668731596684224866951378664 (53 iterations, 28-digits).

Surprisingly, there are palindromic numbers that are themselves Lychrel numbers; the first example is 4994.

How many Lychrel numbers are there below ten-thousand?

NOTE: Wording was modified slightly on 24 April 2007 to emphasise the theoretical nature of Lychrel numbers.
```

这个题目可以预见计算量非常大，对一万个数进行利克瑞尔数判断，使用50次迭代的约束条件。数学上一个准确的利克瑞尔数的判断理论尚不存在，应该是数论领域的内容，一会可以去找找现有的进展。使用C++语言以及Python，下面给出程序：
```c++
#include <iostream>
#include <cmath>
#include <vector>
using namespace std;

vector<int> findDigits(int n){
    vector<int> output;
    while(n>0){output.push_back(n%10);n /=10;}
    return output;
}

vector<int> reverseAndAdd(vector<int> input){
    vector<int> output;int length=input.size();
    for(int k=0;k<length;++k){output.push_back(input[k]+input[length-1-k]);}

    int temp=0;
    for(int k=0;k<output.size()-1;++k){if(output[k]>=10){temp=output[k];output[k]=temp%10;output[k+1]+=temp/10;}}
    while(output.back()>=10){temp=output.back();output[output.size()-1]=temp%10;output.push_back(temp/10);}

    return output;
}

bool isPalindrome(vector<int> input){
    //判断是否为回文数
    for(int k=0;k<=input.size()/2;++k){if(input[k] != input[input.size()-k-1]){return 0;}}
    return 1;
}


int findLychrelNumbers(int upperBound,int iterationBound){
    //寻找符合条件的数，最后返回个数
    //这儿有两个参数，一个是范围，一个是约束
    int iterations, count=upperBound-1;vector<int> data;
    for(int k=1;k<upperBound;++k){
        iterations=0;data=findDigits(k);
        while( (++iterations) < iterationBound){
            data=reverseAndAdd(data);
            if(isPalindrome(data)){--count;break;}
        }
    }
    cout << count<<endl;
    return count;
}



int main (int argc, char * const argv[]) {

    findLychrelNumbers(10000,50);
    return 0;
    //不得不说C语言写起来太繁琐了
}
```
```python
def fanzhuan(n):
    return int(str(n)[::-1])
count=10000
for num in range(10000):
    i = num
    for j in range(50):
        i += fanzhuan(i)
        if i==fanzhuan(i):
            count-=1
            break
print count
##似乎python写起来简单多了:)
```


4(P125),Palindromic sums
-
```
The palindromic number 595 is interesting because it can be written as the sum of consecutive squares: 6^2 + 7^2 + 8^2 + 9^2 + 10^2 + 11^2 + 12^2.

There are exactly eleven palindromes below one-thousand that can be written as consecutive square sums, and the sum of these palindromes is 4164. Note that 1 = 02 + 12 has not been included as this problem is concerned with the squares of positive integers.

Find the sum of all the numbers less than 108 that are both palindromic and can be written as the sum of consecutive squares.
```

```
这个题目不简单，主要的难点在于一个整数怎么取特定的数，然后其平方之和为这个整数，解决了这个问题，其他的便好做。这个题目使用函数式编程语言Haskell解决，下面给出代码：
```
```haskell
import Data.List ( tails )
import Data.Set ( foldl,fromList )

main = let n = 10^8 in print $ Data.Set.foldl (+) 0 $ fromList
   $ filter (\x -> let z = show x in z == reverse z) $ concatMap
   (takeWhile (<n) . drop 1 . scanl1 (+)) $ tails $ takeWhile
      (< div n 2) $ map (^2) [1..]
```

最好理解的python与最难理解的haskell都十分的简短，两种语言都是需要打磨的利器。十年磨一剑，霜刃未曾试；如今把式人，谁有不平事。学东西学到这种境界就可以说是学会了一门语言吧。
