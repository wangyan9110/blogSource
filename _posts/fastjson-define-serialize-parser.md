title: 'FastJson 自定义Serialize、Parser'
date: 2014-07-02 20:32:11
categories: 
	- java
tags: 
	- fastjson
	- java
	- in action
---
今天在处理Json反序列化时，在C#传过来的JSON字符串中枚举类型为int类型，FastJson对于枚举的处理有两种类型，一种是字符串一种是int类型，但是它自带的解析int是按照枚举的顺序来解析的，但是有时候值不一定和顺序相对应，所以使用自定义解析器方式进行解决。在网上找解决方案，没找到比较完整的方法。通过查看源代码得出解决方案。<!--more-->
以解析如下枚举为例：
```java
public enum Status {

	Ready(10),

	Completed(20);

	private int value;

	private Status(int value) {
		this.value = value;
	}

	public static Status create(int value) {
		switch (value) {
		case 10:
			return Status.Ready;
		case 20:
			return Status.Completed;
		default:
			throw new RuntimeException("code error");
		}
	}
	
	public static int value(Status status){
		return status.value;
	}
}
```
反序列化过程实现objectDeserializer接口，以上枚举代码如下：
```java
public class StatusDeserializer implements ObjectDeserializer {

	public <T> T deserialze(DefaultJSONParser parser, Type type,
			Object fieldName) {
		JSONLexer lexer = parser.getLexer();
		int value = lexer.intValue();
		return (T) Status.create(value);
	}

	public int getFastMatchToken() {
		// TODO Auto-generated method stub
		return 0;
	}
}
```
序列化过程实现ObjectSerializer接口
```java
public class StatusSerializer implements ObjectSerializer{

	public void write(JSONSerializer serializer, Object object,
			Object fieldName, Type fieldType) throws IOException {
		SerializeWriter out = serializer.getWriter();
		 if (object == null) {
	            serializer.getWriter().writeNull();
	            return;
	        }
		 out.write(Status.value((Status)object));
	}
}
```
使用自定义的序列化和反序列化的方法如下：
序列化过程
```java
        SerializeConfig config=new SerializeConfig();
    	config.put(Status.class, new StatusSerializer());
    	String jsonStr=JSON.toJSONString(test,config);
```
反序列化过程
```java
        ParserConfig.getGlobalInstance().putDeserializer(Status.class, new StatusDeserializer());
    	Test test1=JSON.parseObject(jsonStr,Test.class);
```
除了处理枚举类型时，有时需要自定义序列化、反序列化方法外，在处理日期、时间等类型时也经常需要自己实现。