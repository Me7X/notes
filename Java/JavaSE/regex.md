# 正则表达式



---

* 获取String中所有的<a>标签

```Java
    String s = "<a href='{AppContextName}.www.baidu.com'>baidu</a><a href='{AppContextName}.www.163.com'>baidu</a>"
	+ "<a href='{AppContextName}.www.qq.com'>baidu</a><a href='{AppContextName}.www.google.com'>baidu</a>";
	String regex = "(<a[\\s+]*([^>h]|h(?!ref\b))*href[\\s+]*=[\\s+]*[('|\")]?)([^(\\s+|'|\")]*)([^>]*>)";
	Pattern p = Pattern.compile(regex);
	Matcher m = p.matcher(s);
	while(m.find()){
		System.out.println("==" + m.group());
	}
	
	//Console
	==<a href='{AppContextName}.www.baidu.com'>
    ==<a href='{AppContextName}.www.163.com'>
    ==<a href='{AppContextName}.www.qq.com'>
    ==<a href='{AppContextName}.www.google.com'>
```

* 获取href

```Java
	String s = "<a href='{AppContextName}.www.baidu.com'>baidu</a><a href='{AppContextName}.www.163.com'>baidu</a>"
		+ "<a href='{AppContextName}.www.qq.com'>baidu</a><a href='{AppContextName}.www.google.com'>baidu</a>";
	String regex =  "(?i)href[\\=\"\']+([^\"\']*)[\"\']?";
	Pattern p = Pattern.compile(regex);
	Matcher m = p.matcher(s);
	while(m.find()){
		System.out.println(m.group());
	}
	
	//Console
	href='{AppContextName}.www.baidu.com'
    href='{AppContextName}.www.163.com'
    href='{AppContextName}.www.qq.com'
    href='{AppContextName}.www.google.com'
```

* 获取value

```Java
	String s = "<a href='{AppContextName}.www.baidu.com&d=1000?c=454 ;789&tye=10%33'>baidu</a><a href='{AppContextName}.www.163.com'>baidu</a>"
		+ "<a href='{AppContextName}.www.qq.com'>baidu</a><a href=\"{AppContextName}.www.google.com&d=1000?c=454; 789&tye=10%33\">baidu</a>";
	String regex =  "[\'|\"]+([^\"\']*)[\"\'|\"]?";
	Pattern p = Pattern.compile(regex);
	Matcher m = p.matcher(s);
	while(m.find()){
		System.out.println(m.group());
	}
	
	//Console
	'{AppContextName}.www.baidu.com&d=1000?c=454 ;789&tye=10%33'
    '{AppContextName}.www.163.com'
    '{AppContextName}.www.qq.com'
    "{AppContextName}.www.google.com&d=1000?c=454; 789&tye=10%33"
```
