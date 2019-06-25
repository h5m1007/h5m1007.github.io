---
layout: post
title: "正则表达式 全攻略"
date: 2016-08-30 18:37
categories: 正则表达式 javascript
tags: 正则 replace() match() test() exec() 
---



正则表达式 帮助您匹配搜索一切您想得到的字符串




## 什么是正则
类似`/(^\s+)|(\s+$)/gmi`，就是所谓的正则表达式。它复杂但并不神秘，一旦掌握后，可谓是如鱼得水。在想查找某些符合复杂匹配规则的文本时，一般是字符串，就会使用正则来描述这类规则，当然你可以使用通配符`*`来查找出所有文本，只是正则能够更精确地描述您的需求。  

g 指全文匹配，当没设置g时，正则匹配只返回**第一次**匹配的结果  
i 指忽略大小写  
m指多行匹配  

() 提取字符串，为正则表达式分组，(?:\b{3}) "?:"用来忽略某个分组，每一组匹配关系由$1...$n一一对应表示  
[] 匹配指定字符任一个，如[abc]表示匹配a字符或b字符，还可定义匹配范围，如[a-z]、\d[1,3]匹配1~3次数字，  
{} 匹配长度如\d{3}匹配3次数字，\d{3,}匹配至少3次数字，\d{0,3}匹配至多3次数字  

(^a)匹配以a开头的字符串  
[^xyz]匹配的字符串不包含xyz  
(b$)匹配以b结尾的字符串  

\d 匹配一个非负整数即[0-9]
\b 匹配单词边界，\B匹配非单词边界，如：" s "  
\s 匹配一个空白字符，\S匹配非空白字符  
\w 匹配一个字母或数字即[0-9a-zA-Z]  
. 匹配除回车符和换行符外的任意字符即[^\r\n]  

星号 匹配前面元素0次或多次, 如(\s*)即匹配0或多个空格;  
加号(+) 匹配前面元素1次或多次, 如(\d+)即匹配由至少1个整数组成的字符串;  
问号(?) 匹配前面元素0或1次, 如(\w?)即匹配最多由1个字母或数字组成的字符串  

(**注： 匹配的过程  正则遍历字符串的每个字符，每次匹配到的字符不再参与下次匹配**)

- replace(正则, 函数)  

当函数fn为回调时，匹配一次结果就回调一次  

每次回调传参有：  
1. result:本次匹配的结果  
2. $1,...$9: 正则有多少 (), 就传递几个参数, $1,...$9表示*本次*匹配中每个()提取的结果  
3. offset: 记录本次匹配的起始位置  
4. source: 接受匹配的原始字符串  

如以下代码应用：

```
// 需把'a1b2c3d4e5' => 'a2b3c4d5e6'
'a1b2c3d4e5'.replace(/\d/g, function(result, offset, source){
    console.log(index);
    return parseInt(match) + 1; // 返回值将替换匹配结果
});

// 需把'a1b2c3d4e5' => 'a12c34e5'
'a1b2c3d4e5'.replace(/(\d)(\w)(\d)/g, function(result, $1, $2, $3, offset, source){
    console.log(result);
    return $1 + $3; // 返回值将替换匹配结果
});
```

- test()/exec()  
    test(str): 检测字符串是否匹配某一个正则, 如 /\d+/.test("123") 返回true  

    如：`reg = /\w/`  则有`reg.global = false; reg.ignoreCase = false; reg.multiline = false;`  
    其中，正则有这样一个属性**lastIndex**来表示*当前匹配结果的最后一个字符的**下一个字符***，并在如下代码中得到应用  

```
while(reg.test('ab')){
    console.log(reg.lastIndex); // 将得到 1 2 
}
```

所以，也因有这样一个属性，会让上述while循环中，得到lastIndex为2后将中断循环  

正则表达式对象的exec(str)方法，用来返回匹配过程的所有信息，如：  
1. **RegExpObject.exec(str)**  返回数组，存放每次匹配到的字符(全局g)，及子表达式匹配的字符(类似由"()"分组的)  
2. **exec(str).index**  返回第一次匹配首字符的位置(下标)  

如下代码应用：  

```
var reg3 = /\d(\w)\d/;
var reg4 = /\d(\w)\d/g;
var ts = '1a2b3c4d5e';

var ret = reg3.exec(ts);

console.log(reg3.lastIndex + '\t' + ret.index +
            '\t' + ret.toString());
            // 0  0  1a2,a

while(ret = reg4.exec(ts)){
  console.log(reg4.lastIndex + '\t' + ret.index +
             '\t' + ret.toString());
             // 3  0  1a2,a
             // 7  4  3c4,c
}
```

- match()  
    str.match(reg): 获取正则匹配到的结果且以数组返回, 如 "189a689v28".match(/\d+/g) 返回 ["189","689","28"]  

    在非全局g的情况下，与exec(str)方法用法相同且返回值也相同  

    在全局g的情况下，当匹配到结果时str.match(reg)只返回*一个数组*来存放每次结果，而exec()经while()循环会得到每次匹配到的结果的*多个数组*，且没有index和input属性  
