---
title: Hoare's algorithm
date: 2018-04-30 13:08:28
author: Lloyd
categories: [算法]
tags: [快速选择]
---

快速选择算法通常用来在未排序的数组中寻找第k小/第k大的元素。其方法类似于快速排序。快速选择和快速排序都是Tony Hoare发明的，因此也称为Hoare's algorithm。

下面是Madagascar中对快速选择算法的实现部分：
```
float sf_quantile(int q    /* quantile */,
		  int n    /* array length */,
		  float* a /* array [n] */)
/*< find quantile (caution: a is changed) >*/
{
    float *i, *j, ak, *low, *hi, buf, *k;

    low=a;
    hi=a+n-1;
    k=a+q;
    while (low<hi) {
	    ak = *k;
	    i = low; j = hi;
	    do {
        // 找出a[i]>=ak 时的i值
	      while (*i < ak) i++;
        // 找出a[j]>=ak 时的j值     
	      while (*j > ak) j--;   
        // 交换 a[i]与a[j]的值，并移动i和j  
	      if (i<=j) {
		      buf = *i;
		      (*i)++ = *j;
		      (*j)-- = buf;
	      }
	    } while (i<=j);
      // 更新low 和 hi
	    if (j<k) low = i;
	    if (k<i) hi = j;
    }
    return (*k);
}
```

## 参考资料
[Quick Select Algorithm快速选择算法](https://blog.csdn.net/Yaokai_AssultMaster/article/details/68878950)

[选取第K大数的快速选择算法和注意事项](https://comzyh.com/blog/archives/713/)

[维基百科](https://zh.wikipedia.org/wiki/快速选择)

[Madagascar](https://github.com/ahay/src/blob/master/api/c/quantile.c)


