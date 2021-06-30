# 仿函数：
网上也没有太过于贴合实际场景的理解，更多的是针对于数组排序或者统计中的参数传递进行对比讲解；

仿函数实际上就是用类+重载“（）”操作符，达到复用的目的；
设想一个统计函数，我们需要统计一个数组中大于threhold的个数；
通常来说，需要两个函数：
1.bool判断函数，返回true和false；
2.主题函数，用于统计和循环调用判断函数；

问题就在于，当我们需要灵活设计threhold值的时候，需要在判断函数中修改，或者额外传参，这样就会很麻烦，所以如果采用仿函数可以更好的消除这一点问题；

例如，对于类的初始化，我们可以这样设定：
```C++
class fun {
public:
	explicit fun(int h):therhold(h) {
	}
	bool operator() (int n) {
		if (n > therhold)
			return true;
		else
			return false;
	}
private:
	int therhold;
};
```
如上所示，我们可以直接采用fun(threhold)来进行初始化，得到我们想要的阈值判别；
剩余遍历，不用改变，直接在逻辑中调用fun(vector[xxx])即可进行bool判断；
```C++
class fun {
public:
	explicit fun(int h):therhold(h) {
	}
	bool operator() (int n) {
		if (n > therhold)
			return true;
		else
			return false;
	}
private:
	int therhold;
};

int charge(vector<int>& vec, fun f) {
	int cnt = 0;
	for (auto it = vec.begin(); it < vec.end(); it++) {
		if (f(*it))
			cnt++;
	}
	return cnt;
}
int cnt = charge(vec, fun(threhold));
```

在标准库中也用到了许多类似操作，升值可以对类进行继承，来更多的自定义化内容；

# lambda表达式：
之前在Qt中经常见到，这里做一个小结；
lambda表达式具有两个功能：
1.进行一个小功能函数，削减函数的定义和使用成本；
2.有延迟生效的功能，即，我实际发动一个函数，但是先不使用，后续再调用，不同于静态定义，之后需要再调用的行为；

标准写法如下：
[ capture ] ( params ) opt -> ret { body; };
其中：
captrue是捕获形式，其中&为引用捕获，=为拷贝捕获；
![image-20210630203413954](C:\Users\Innovation\AppData\Roaming\Typora\typora-user-images\image-20210630203413954.png)
params为参数列表；
ret为返回类型，其中对于简单的内置形式，ret可以不需要指定，类似于auto；

因此，在一些简单的排序中，我们可以用如下形式：
```C++
for_each(vec.begin(), vec.end(), [&threhold,&cnt](int val) {
		if (val > threhold)
			cnt++;
		});
```

其中更需要注意的是，lambda可以延迟调用；
其中当lambda表达式作为变量进行接收后，会直接当场捕获值；
如果采用复制捕获，则无论后面所捕获的值在外部如何变化，都不会改变，因为已经发动。
如果想让捕获的值随着外部变化而变化，实时动态更新，则需要&进行引用捕获；

## 补充：
lambda其实就是仿函数的一种，网上有如下的注释：
![image-20210630204753438](C:\Users\Innovation\AppData\Roaming\Typora\typora-user-images\image-20210630204753438.png)

后续还是要补齐一下闭包和匿名类的构造和析构问题；
