---
layout: post
title: ruby中的block
date: 2022-03-08 10:07 +0800
excerpt: ruby中block, proc, lambda概念有点绕, 举个场景来讲一下这中设计的可能原因，帮助理解
---

# Block, Proc, Lambda
先贴一下基本概念，这些概念我一直没有搞太清楚，单纯去记忆太费脑子了。不过通过后面的例子，大家应该能轻松的区别这3概念。

1. Block是一类，Proc和Lambda是另外一类。
2. Proc 和 Lambda 都是对象，而 Block 不是
3. 参数列表中最多只能有一个 Block，但是可以有多个 Proc 或 Lambda
4. Proc和Lambda都是Proc对象
5. Lambda 对参数的检查很严格，而 Proc 则比较宽松
6. Proc 和 Lambda 中return关键字的行为是不同的
   1. 在lambda中，returen是指从lambda对象返回；
   2. 在proc中，returen不是从proc返回，而是从定义proc的作用域返回。

我已经开始头晕了。

# Block的简单理解

你可以理解为C/C++, Java, C#或者任何常见语言里面的代码块.
Block前面的函数调用理解为`while`,`for`循环之类的条件判断语句.
~~~c++

void func1() {
	int a = 0;
	while(a++ < 10) {
		cout << "hello" << endl;;
		if(a == 8) return a; // 注意这地方，这个return效果和下面ruby block里面return效果一样的, 退出当前方法。
	}
}

~~~

和下面效果一样.
~~~ruby
def condition(a)
    while a < 10 do
	    yield a
		a+=1
	end
end

def func1()
	condition(1) {|a|
		puts a
		return a if a == 8; # 注意这地方，这个return效果和下面C++里面return效果一样的, 退出当前方法。
	}
end
~~~
可以简单理解ruby block和C++的block或者编辑器基本的block基本上一样，不过ruby的block还能接受参数。

## Block举例
下面举的一个例子是作为配置管理工具, 我们希望在`dev`和`prod`两种不同环境下有不同的处理逻辑。

~~~ruby
    def self.in_dev call_index
	  return unless self.dev
      puts  "in dev #{call_index}"
      yield
      puts "return from dev #{call_index}"
    end

    def self.in_prod
	  return unless self.prod
      puts "in production"
      yield
      puts "return from production"
    end

    def self.condition_test
	  self.dev = true
      self.in_dev(1) {  puts "{in_dev with block1}" }
      self.in_dev(2) {  puts "{in_dev with block2}"; return  "in_dev"}
      self.in_prod {return 'bbb' }
    end
~~~

output
~~~sh
in dev 1
{in_dev with block1}
return from dev 1
in dev 2
{in_dev with block2}
~~~

可以看到，如果当前环境是`dev`环境，`condition_test`只会执行第`2`,`3`两条语句， 因为第3条语句会`return`. 如果是prod环境, 只会执行第`4`条语句`self.in_prod {return 'bbb' }`.

# Proc和Lambda
目前没有找到很好的场景去理解它们俩。
