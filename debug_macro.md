一键开关穿插在代码里的打印符

```cpp

//#define NDEBUG
//https://stackoverflow.com/a/1389600/13792395
#ifdef NDEBUG
#define out std::cout
#else
#define out NullStream()
#endif
class NullStream {
public:
	NullStream() { }
	template<typename T> NullStream& operator<<(T const&) { return *this; }
};

int main()
{

	vector<int> sa{ 1,2,3,45 };
	out << sa<<"\n";
}
```

这里有一个更优化版本，暂时不知道他的```operator<<(R& (*pf)(P&)) ``` 是干嘛的
https://stackoverflow.com/a/1389813/13792395

```cpp

class MyDebug
{
	std::ostream& stream;
public:
	MyDebug(std::ostream& s) : stream(s) {}

#ifdef NDEBUG
	template<typename T>
	MyDebug& operator<<(T& item)
	{
		stream << item;
		return *this;
	}
	MyDebug& operator<<(std::ostream& (*pf)(std::ostream&))
	{
		stream << pf;
		return *this;
	}
	template<typename R, typename P>
	MyDebug& operator<<(R& (*pf)(P&))	//<== 他是干嘛的
	{
		cout << "never called!";
		stream << pf;
		return *this;
	}
#else
	template<typename T>
	MyDebug& operator<<(T&)
	{
		return *this;
	}
#endif
};
int main()
{
	MyDebug md(cout);
	vector<int> sa{ 1,2,3,45 };
	md<< sa<< flush;
}

```

更传统的做法是这样，好处是编译时候这些代码绝对不会出现。

```cpp
#ifdef DEBUG
#  define IFDBG(x) x
#else
#  define IFDBG(x)
#endif
```
IFDBG(cout << result << endl);