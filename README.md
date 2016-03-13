
一个基于NFA的正则表达式引擎,语法和接口基本与python的re模块一致  
---

usage:
--- 
```cpp
string regular_expression = u8"regular[123]";  
//作为正则表达式的字符串用utf8编码 string 来表示, 支持中文, 引擎内部统一使用
//ucs2（即utf16) 来储存

auto t = rgx::complie (regular_expression);
// compile 返回一个编译后的正则表达式, compile的返回类型为 unique_ptr<_pattern>

auto matchobj t->match(u8"input", startposition);
// 使用编译之后的_pattern来匹配输入串，同理输入串使用utf8编码的 string 来表示
// 使用match方法将返回unique_ptr<matchObj>

cout << ucs2_to_string(matchobj.group(0)) << endl;
//使用matchObj的group() 方法来获取捕获的分组，0 号分组表示整个捕获到的字符串，
//group方法返回utf16的u16string, 可以用usc2_to_string来转换为等价的string
```
---


详细语法说明: 
---

###字符类
- .  
匹配除\n之外的任意字符
- \  
转义字符
- [..]  
字符集，可逐个列出，可用'-'表示范围，第一个字符如果为'^'表示取反，字符集中除非第一个字符的'-',开头的 '^',和']'之外，其他特殊字符均失去其           
- \d  
字符类，匹配数字
- \D  
字符类，匹配非数字
- \s  
字符类，匹配空白字符
- \S  
字符类，匹配非空白字符
- \w  
字符类，匹配字母或数字
- \W  
字符类，匹配非字母及非数字

###重复 
- *  
表示循环 0 至无限多次
- +  
表示循环 1 至无限多次
- ?  
表示循环 0 次或 1 次
- {m}  
表示循环m次
- {m, n}  
表示循环 m 至 n 次
- \*?, +? {m, n}?, ??  
表示非贪婪，默认为贪婪

###位置字符 
- ^  
匹配字符串的开头，在多行模式中匹配某一行的开头
- $  
匹配字符串结尾, 多行模式中匹配某一行的结尾
- \A  
仅匹配字符串开头
- \Z
仅匹配字符串的结尾

###捕获
> 注：捕获只能捕获最后一次匹配时遇见的字符，如用'abcfg' 来匹配表达式 '(a|b|c)\*fg'，分组 1 捕获到的内容只有'c'， 而不是'abc'， 同理，引用也只能引用最近一次的捕获内容

- |  
表示或
- (?: )  
分组但是不捕获，用于 '|' 以及循环
- ()  
匿名捕获, 分组并且捕获括号中的内容，分组编号从0 开始，第 0 组表示整个匹配到的字符串
- (?P<nam    e>)  
具名捕获，捕获并为分组设定别名name
- \number  
匿名引用，引用分组为number的捕获内容，number不能为0
- (?P=nam    e)  
具名引用, 引用别名为name 的分组

###预查
> 注：用于预查的表达式的语法使用纯正则表达式的语法，不再支持任何扩展，如引用,捕获等扩展

- (?= )  
正向预查，之后的字符必须符合括号中的表达式，
- (?! )  
正向预查，之后的字符必须不符合括号中的表达式才能匹配成功
- (?<= )  
逆向预查，之前的字符必须符合括号中的表达式才能匹配成功
- (?<\! )  
逆向预查, 之后的字符必须不符合括号中的表达式才能匹配成功
