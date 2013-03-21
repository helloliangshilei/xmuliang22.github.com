--- 
layout: post
title: 算法学习一（大数运算）
categories:
    - algorithm
tags:
    - big num
    - algorithm
---

#大数加法
- 题目来源：[xmuoj 1006](http://acm.xmu.edu.cn/JudgeOnline/problem.php?id=1006)
大数加法，是将计算机无法直接表示的大数存储在整形数组里，如a[1001]可以表示1001位的整数。运算过程中将对应位的数依次相加，大于10的向前进位。另外一点就是对与相加的两个数的长度不同时，本算法做了分别处理，不够优化，希望能进一步改进。
- 代码：

/*

Author:Liang Shilei

Time:2013-03-19

Function:大数加法

*/

`#include<iostream>`

`#include<string>`

using namespace std;

static int flag=0;

int main()

{

	int a[1010],b[1010],c[1010];

	int tmp,i;



	string str1,str2;

	cin>>str1>>str2;

	int len1=str1.size();

	int len2=str2.size();

	for(i=0;i<len1;i++)

		a[i]=str1[i]-'0';

	for(i=0;i<len2;i++)

		b[i]=str2[i]-'0';

	if(len1>len2)

	{

		for(i=len2-1;i>=0;i--)

		{

			tmp=len1-len2+i;

			c[tmp]=a[tmp]+b[i];

			if(c[tmp]>=10)

			{

				c[tmp]=c[tmp]%10;

				a[tmp-1]+=1;

			}

		}

		for(i=len1-len2-1;i>=0;i--)

		{

			c[i]=a[i]%10;

			if(a[i]>=10)

				a[i-1]++;

		}

		c[0]=a[0]%10;

		if(a[0]>=10)

		{	

			cout<<"1";

			for(i=0;i<len1;i++)

				cout<<c[i];

			cout<<endl;

		}

		else

		{

			for(i=0;i<len1;i++)

				cout<<c[i];

			cout<<endl;

		}

	}

	else if(len1<len2)

	{

		for(i=len1-1;i>=0;i--)

		{

			tmp=len2-len1+i;

			c[tmp]=b[tmp]+a[i];

			if(c[tmp]>=10)

			{

				c[tmp]=c[tmp]%10;

				b[tmp-1]+=1;

			}

		}

		for(i=len2-len1-1;i>0;i--)

		{

			c[i]=b[i]%10;

			if(b[i]>=10)

				b[i-1]++;

		}

		c[0]=b[0]%10;

		if(b[0]>=10)

		{

			cout<<"1";

			for(i=0;i<len2;i++)

				cout<<c[i];

			cout<<endl;

		}

		else

		{

			for(i=0;i<len2;i++)

				cout<<c[i];

			cout<<endl;

		}

	}

	else

	{

		for(i=len1-1;i>=1;i--)

		{

			c[i]=a[i]+b[i];

			if(c[i]>=10)

			{

				c[i]%=10;

				a[i-1]++;

			}

		}

		c[0]=a[0]+b[0];

		if(c[0]>=10)

		{

			c[0]%=10;

			cout<<"1";

			for(i=0;i<len1;i++)

				cout<<c[i];

			cout<<endl;

		}

		else

		{

			for(i=0;i<len1;i++)

				cout<<c[i];

			cout<<endl;

		}

	}

	return 0;

}

#大数乘法

- 题目来源：[xmuoj1007](http://acm.xmu.edu.cn/JudgeOnline/problem.php?id=1007)
大数的表示放法同上，同样采用整形数组的形式，另外也可以采用字符串的形式来存储。做乘法运算时从高位向地位做乘法运算，暂时将运算的结果保存在相应的位里。运算完毕以后，对各个为进行进位运算（大数加法也可以采用此种思路），过程比较简洁。并且两个数的乘法的结果的长度要么是两个数的长度之和，要么是长度之和减一.

- 代码：
/*

Author:Liang Shilei

Time:2013-03-20

Function:大数乘法

*/



#include<iostream>

#include<string>

#include<vector>

using namespace std;

int main()

{

	vector<int> a,b;//采用容器来表示整数

	string str1,str2;

	cin>>str1>>str2;

	int i,j,k,len1,len2;

	len1=str1.size();

	len2=str2.size();



	vector<int> c(len1+len2-1);

	

	for(i=0;i<len1;i++)

		a.push_back(str1[i]-'0');

	for(i=0;i<len2;i++)

		b.push_back(str2[i]-'0');



	for(i=0;i<len1;i++)

	{

		k=i;

		for(j=0;j<len2;j++)

			c[k++]+=a[i]*b[j];//key point

	}



	for(i=c.size()-1;i>0;i--)

	{

		if(c[i]>=10)

		{

			c[i-1]+=c[i]/10;

			c[i]%=10;

		}

	}



	int tmp;

	if(c[0]>=10)

	{

		tmp=c[0]%10;

		c.insert(c.begin(),c[0]/10);

		c[1]=tmp;

		

	}



	if(c[0]==0)//去除结果为“000”类似的输出

		cout<<"0"<<endl;

	else

	{

	for(i=0;i<c.size();i++)

		cout<<c[i];

	cout<<endl;

	}

	return 0;

}

		








































































