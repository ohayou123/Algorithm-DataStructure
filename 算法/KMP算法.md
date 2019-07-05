# 目录
[toc]

## 介绍

KMP算法是根据三位作者（D.E.Knuth，J.H.Morris和V.R.Pratt）的名字来命名的，算法的全称是Knuth Morris Pratt算法，简称为KMP算法。

BM算法有两个规则，坏字符和好后缀。KMP算法借鉴BM算法的思想，可以总结成好前缀规则。这里面最难懂的就是next数组的计算。如果用最笨的方法来计算，确实不难，但是效率会比较低。所以，我讲了一种类似动态规划的方法，按照下标i从小到大，依次计算next[i]，并且next[i]的计算通过前面已经计算出来的next[0]，next[1]，……，next[i-1]来推导。

### 适用场景

KMP：适合所有场景，整体实现起来也比BM简单，O(n+m)，仅需一个next数组的O(n)额外空间；但统计意义下似乎BM更快，原因不明.

### 最大公共元素确定下标
如果给定的模式串是：“ABCDABD”，从左至右遍历整个模式串，其各个子串的前缀后缀分别如下表格所示：![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522164014.png)


```
所以，当失配时，模式串向右移动的位数为：
已匹配字符数 - 失配字符的上一位字符所对应的最大长度值。
```

---

如果要匹配

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522164218.png)

因为模式串中的字符A跟文本串中的字符B、B、C、空格一开始就不匹配，所以不必考虑结论，直接将模式串不断的右移一位即可，直到模式串中的字符A跟文本串的第5个字符A匹配成功：

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522164249.png)

因为此时已经匹配的字符数为6个（ABCDAB），然后根据《最大长度表》可得失配字符D的上一位字符B对应的长度值为2，所以根据之前的结论，可知需要向右移动6 - 2 = 4 位。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522164305.png)
模式串向右移动4位后，发现C处再度失配，因为此时已经匹配了2个字符（AB），且上一位字符B对应的最大长度值为0，所以向右移动：2 - 0 =2 位。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522164335.png)

A与空格失配，向右移动1 位。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522164350.png)

继续比较，发现D与C 失配，故向右移动的位数为：已匹配的字符数6减去上一位字符B对应的最大长度2，即向右移动6 - 2 = 4 位。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522164405.png)

经历第5步后，发现匹配成功，过程结束。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522164415.png)



上面的例子很好找到位移，但是这样代码计算量太过于繁琐，我们可以采用这个思想：问题的关键就是寻找模式串中最大长度的相同前缀和后缀，找到了模式串中每个字符之前的前缀和后缀公共部分的最大长度后，便可基于此匹配。而这个最大长度便正是next 数组要表达的含义。（KMP算法）


## KMP算法的核心思想
跟BM算法非常相近。我们假设主串是a，模式串是b。在模式串与主串匹配的过程中，当遇到不可匹配的字符的时候，++我们希望找到一些规律，可以将模式串往后多滑动几位，跳过那些肯定不会匹配的情况++。

里我们可以类比一下，在模式串和主串匹配的过程中，把不能匹配的那个字符仍然叫作坏字符，把已经匹配的那段字符串叫作好前缀。![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522174120.png)

KMP算法就是在试图寻找一种规律：在模式串和主串匹配的过程中，当遇到坏字符后，对于已经比对过的好前缀，能否找到一种规律，将模式串一次性滑动很多位？

### KMP思路


假设坏字符所在位置是 j，最长可匹配后缀子串长度为 k，则模式串需要后移的位数为 j-k。每当我们遇到坏字符，就将模式串后移 j- k 位，直到模式串与对应主串字符完全匹配；如果移到最后还是不匹配，则返回 -1。这就是 KMP 算法的核心思想。

>假设最长的可匹配的那部分前缀子串是{v}，长度是k。我们把模式串一次性往后滑动j-k位，相当于，每次遇到坏字符的时候，我们就把j更新为k，i不变，然后继续比较。![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522165721.png)


>先介绍概念：
- 后缀子串：以某个字符串最后一个字符为尾字符的子串（不包含字符串自身），比如上面的 ababa，后缀子串为 baba、aba、ba、a；
- 前缀子串：以某个字符串第一个字符为首字符的子串（不包含字符串自身），还是以 ababa 为例，前缀子串为 a、aba、abab；
- 最长可匹配后缀子串：后缀子串与前缀子串最长可匹配子串，也可叫做共有子串，以 ababa 为例，自然是 aba 了，长度为3；
- 最长可匹配前缀子串：与上面定义相对，即前缀子串与后缀子串最长可匹配子串。最长可匹配前缀子串和最长可匹配后缀子串肯定是一样的。
- 我们把好前缀的所有后缀子串中，最长的可匹配前缀子串的那个后缀子串，叫作最长可匹配后缀子串；对应的前缀子串，叫作最长可匹配前缀子串。![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522165743.png)




了解了核心思想，接下来，就可以考虑如何实现 KMP 算法了，实现 KMP 算法最核心的部分是构建一个用来存储模式串中每个前缀子串（这些前缀都有可能是好前缀）最长可匹配前缀子串的结尾字符下标数组，我们把这个数组叫做 next 数组，对于上面 ABCDABD 这个模式串而言，对应的 next 数组如下：

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522193134.png)






#### 最大长度表求出了next 数组

next 数组相当于“最大长度值” 整体向右移动一位，然后初始值赋为-1。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522191321.png)

#### 通过代码递推计算next 数组

1. 如果对于值k，已有p0 p1, ..., pk-1 = pj-k pj-k+1, ..., pj-1，相当于next[j] = k。

- 究其本质，next[j] = k 代表p[j] 之前的模式串子串中，有长度为k 的相同前缀和后缀。有了这个next 数组，在KMP匹配中，当模式串中j 处的字符失配时，下一步用next[j]处的字符继续跟文本串匹配，相当于模式串向右移动j - next[j] 位。
    
2. 已知next [0, ..., j]，如何求出next [j + 1]呢？
    
对于P的前j+1个序列字符：
- 若p[k] == p[j]，则next[j + 1 ] = next [j] + 1 = k + 1；
    
- 若p[k ] ≠ p[j]，如果此时p[ next[k] ] == p[j ]，则next[ j + 1 ] =  next[k] + 1，否则继续递归前缀索引k = next[k]，而后重复此过程。 相当于在字符p[j+1]之前不存在长度为k+1的前缀"p0 p1, …, pk-1 pk"跟后缀“pj-k pj-k+1, …, pj-1 pj"相等，那么是否可能存在另一个值t+1 < k+1，使得长度更小的前缀 “p0 p1, …, pt-1 pt” 等于长度更小的后缀 “pj-t pj-t+1, …, pj-1 pj” 呢？如果存在，那么这个t+1 便是next[ j+1]的值，此相当于利用已经求得的next 数组（next [0, ..., k, ..., j]）进行P串前缀跟P串后缀的匹配。


假定给定模式串ABCDABCE，且已知next [j] = k（相当于“p0 pk-1” = “pj-k pj-1” = AB，可以看出k为2），现要求next [j + 1]等于多少？因为pk = pj = C，所以next[j + 1] = next[j] + 1 = k + 1（可以看出next[j + 1] = 3）。代表字符E前的模式串中，有长度k+1 的相同前缀后缀。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522191752.png)

但如果pk != pj 呢？说明“p0 pk-1 pk”  ≠ “pj-k pj-1 pj”。换言之，当pk != pj后，字符E前有多大长度的相同前缀后缀呢？很明显，因为C不同于D，所以ABC 跟 ABD不相同，即字符E前的模式串没有长度为k+1的相同前缀后缀，也就不能再简单的令：next[j + 1] = next[j] + 1 。所以，咱们只能去寻找长度更短一点的相同前缀后缀。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522191817.png)

若能在前缀“ p0 pk-1 pk ” 中不断的递归前缀索引k = next [k]，找到一个字符pk’ 也为D，代表pk’ = pj，且满足p0 pk'-1 pk' = pj-k' pj-1 pj，则最大相同的前缀后缀长度为k' + 1，从而next [j + 1] = k’ + 1 = next [k' ] + 1。否则前缀中没有D，则代表没有相同的前缀后缀，next [j + 1] = 0。

那为何递归前缀索引k = next[k]，就能找到长度更短的相同前缀后缀呢？这又归根到next数组的含义。我们拿前缀 p0 pk-1 pk 去跟后缀pj-k pj-1 pj匹配，如果pk 跟pj 失配，下一步就是用p[next[k]] 去跟pj 继续匹配，如果p[ next[k] ]跟pj还是不匹配，则需要寻找长度更短的相同前缀后缀，即下一步用p[ next[ next[k] ] ]去跟pj匹配。此过程相当于模式串的自我匹配，所以不断的递归k = next[k]，直到要么找到长度更短的相同前缀后缀，要么没有长度更短的相同前缀后缀。如下图所示：

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522191909.png)


```
void GetNext(char* p,int next[])
{
	int pLen = strlen(p);
	next[0] = -1;
	int k = -1;
	int j = 0;
	while (j < pLen - 1)
	{
		//p[k]表示前缀，p[j]表示后缀
		if (k == -1 || p[j] == p[k]) 
		{
			++k;
			++j;
			next[j] = k;
		}
		else 
		{
			k = next[k];
		}
	}
}

```

```
失配时，模式串向右移动的位数为：
失配字符所在位置 - 失配字符对应的next 值
```
#### 例子
- 如果j = -1，或者当前字符匹配成功（即S[i] == P[j]），都令i++，j++，继续匹配下一个字符；
- 如果j != -1，且当前字符匹配失败（即S[i] != P[j]），则令 i 不变，j = next[j]。此举意味着失配时，模式串P相对于文本串S向右移动了j - next [j] 位。
    - 换言之，当匹配失败时，模式串向右移动的位数为：失配字符所在位置 - 失配字符对应的next 值，即移动的实际位数为：j - next[j]，且此值大于等于1。”

```
文本串: BBC ABCDAB ABCDABCDABDE
模式串：ABCDABD
```
P[0]跟S[0]匹配失败
所以执行“如果j != -1，且当前字符匹配失败（即S[i] != P[j]），则令 i 不变，j = next[j]”，所以j = -1，故转而执行“如果j = -1，或者当前字符匹配成功（即S[i] == P[j]），都令i++，j++”，得到i = 1，j = 0，即P[0]继续跟S[1]匹配。
P[0]跟S[1]又失配，j再次等于-1，i、j继续自增，从而P[0]跟S[2]匹配。
P[0]跟S[2]失配后，P[0]又跟S[3]匹配。
P[0]跟S[3]再失配，直到P[0]跟S[4]匹配成功，开始执行此条指令的后半段：“如果j = -1，或者当前字符匹配成功（即S[i] == P[j]），都令i++，j++”。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522190840.png)

 P[1]跟S[5]匹配成功，P[2]跟S[6]也匹配成功, ...，直到当匹配到P[6]处的字符D时失配（即S[10] != P[6]），由于P[6]处的D对应的next 值为2，所以下一步用P[2]处的字符C继续跟S[10]匹配，相当于向右移动：j - next[j] = 6 - 2 =4 位。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522192252.png)

向右移动4位后，P[2]处的C再次失配，由于C对应的next值为0，所以下一步用P[0]处的字符继续跟S[10]匹配，相当于向右移动：j - next[j] = 2 - 0 = 2 位。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522192330.png)

移动两位之后，A 跟空格不匹配，模式串后移1 位。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522192343.png)

P[6]处的D再次失配，因为P[6]对应的next值为2，故下一步用P[2]继续跟文本串匹配，相当于模式串向右移动 j - next[j] = 6 - 2 = 4 位。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522192357.png)

匹配成功，过程结束。

![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522192409.png)



[来源](https://blog.csdn.net/v_july_v/article/details/7041827)
### 失效函数计算方法
KMP算法的基本原理讲完了，我们现在来看最复杂的部分，也就是next数组是如何计算出来的？

先根据上面的思想写出代码框架：
```
// 这个算法本质是找相等的最长匹配前缀和最长匹配后缀。
// a, b分别是主串和模式串；n, m分别是主串和模式串的长度。
public static int kmp(char[] a, int n, char[] b, int m) {
  int[] next = getNexts(b, m);//通过模式串得到next数组
  int j = 0;
  for (int i = 0; i < n; ++i) { //主串
    while (j > 0 && a[i] != b[j]) { // 如果b[i-1]的最长前缀下一个字符与b[i]相等，则next[i]=next[i-1]+1.
      j = next[j - 1] + 1;
    }
    //如果不相等，则我们假设找到b[i-1]的最长前缀里有b[0,y]与后缀的子串b[z,i-1]相等，然后只要b[y+1]与b[i]相等，那么b[0,y+1]就是最长匹配前缀。这个y可以不断的通过迭代缩小就可以找到
    if (a[i] == b[j]) {
      ++j;
    }
    if (j == m) { // 找到匹配模式串的了
      return i - m + 1;
    }
  }
  return -1;
}
```
>当然，我们可以用非常笨的方法，比如要计算下面这个模式串b的next[4]，我们就把b[0, 4]的所有后缀子串，从长到短找出来，依次看看，是否能跟模式串的前缀子串匹配。很显然，这个方法也可以计算得到next数组，但是效率非常低。有没有更加高效的方法呢？![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190522174357.png)


这里的处理非常有技巧，类似于动态规划。不过，动态规划我们在后面才会讲到，所以，我这里换种方法解释，也能让你听懂。

我们按照下标从小到大，依次计算next数组的值。当我们要计算next[i]的时候，前面的next[0]，next[1]，……，next[i-1]应该已经计算出来了。利用已经计算出来的next值，我们是否可以快速推导出next[i]的值呢？

如果next[i-1]=k-1，也就是说，子串b[0, k-1]是b[0, i-1]的最长可匹配前缀子串。如果子串b[0, k-1]的下一个字符b[k]，与b[0, i-1]的下一个字符b[i]匹配，那子串b[0, k]就是b[0, i]的最长可匹配前缀子串。所以，next[i]等于k。但是，如果b[0, k-1]的下一字符b[k]跟b[0, i-1]的下一个字符b[i]不相等呢？这个时候就不能简单地通过next[i-1]得到next[i]了。这个时候该怎么办呢？
![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190517211738.png)
我们假设b[0, i]的最长可匹配后缀子串是b[r, i]。如果我们把最后一个字符去掉，那b[r, i-1]肯定是b[0, i-1]的可匹配后缀子串，但不一定是最长可匹配后缀子串。所以，既然b[0, i-1]最长可匹配后缀子串对应的模式串的前缀子串的下一个字符并不等于b[i]，那么我们就可以考察b[0, i-1]的次长可匹配后缀子串b[x, i-1]对应的可匹配前缀子串b[0, i-1-x]的下一个字符b[i-x]是否等于b[i]。如果等于，那b[x, i]就是b[0, i]的最长可匹配后缀子串。![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190517211752.png)可是，如何求得b[0, i-1]的次长可匹配后缀子串呢？次长可匹配后缀子串肯定被包含在最长可匹配后缀子串中，而最长可匹配后缀子串又对应最长可匹配前缀子串b[0, y]。于是，查找b[0, i-1]的次长可匹配后缀子串，这个问题就变成，查找b[0, y]的最长匹配后缀子串的问题了。![](https://raw.githubusercontent.com/binbinbin5/myPics/master/imgs/20190517211805.png)

按照这个思路，我们可以考察完所有的b[0, i-1]的可匹配后缀子串b[y, i-1]，直到找到一个可匹配的后缀子串，它对应的前缀子串的下一个字符等于b[i]，那这个b[y, i]就是b[0, i]的最长可匹配后缀子串。

前面我已经给出KMP算法的框架代码了，现在我把这部分的代码也写出来了。这两部分代码合在一起，就是整个KMP算法的代码实现。


```
// b表示模式串，m表示模式串的长度
private static int[] getNexts(char[] b, int m) {
  int[] next = new int[m];
  next[0] = -1;
  int k = -1;
  for (int i = 1; i < m; ++i) {
    while (k != -1 && b[k + 1] != b[i]) {
      k = next[k];
    }
    if (b[k + 1] == b[i]) {
      ++k;
    }
    next[i] = k;
  }
  return next;
}
```

## KMP算法复杂度分析

空间复杂度很容易分析，KMP算法只需要一个额外的next数组，数组的大小跟模式串相同。所以空间复杂度是O(m)，m表示模式串的长度。

>KMP算法包含两部分，第一部分是构建next数组，第二部分才是借助next数组匹配。所以，关于时间复杂度，我们要分别从这两部分来分析。

1. 我们先来分析第一部分的时间复杂度。

    计算next数组的代码中，第一层for循环中i从1到m-1，也就是说，内部的代码被执行了m-1次。for循环内部代码有一个while循环，如果我们能知道每次for循环、while循环平均执行的次数，假设是k，那时间复杂度就是O(k*m)。但是，while循环执行的次数不怎么好统计，所以我们放弃这种分析方法。

    我们可以找一些参照变量，i和k。i从1开始一直增加到m，而k并不是每次for循环都会增加，所以，k累积增加的值肯定小于m。而while循环里k=next[k]，实际上是在减小k的值，k累积都没有增加超过m，所以while循环里面k=next[k]总的执行次数也不可能超过m。因此，next数组计算的时间复杂度是O(m)。

2. 我们再来分析第二部分的时间复杂度。分析的方法是类似的。

    i从0循环增长到n-1，j的增长量不可能超过i，所以肯定小于n。而while循环中的那条语句j=next[j-1]+1，不会让j增长的，那有没有可能让j不变呢？也没有可能。因为next[j-1]的值肯定小于j-1，所以while循环中的这条语句实际上也是在让j的值减少。而j总共增长的量都不会超过n，那减少的量也不可能超过n，所以while循环中的这条语句总的执行次数也不会超过n，所以这部分的时间复杂度是O(n)。

3. 所以，综合两部分的时间复杂度，KMP算法的时间复杂度就是O(m+n)。
