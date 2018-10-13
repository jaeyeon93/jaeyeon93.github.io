---
layout: post
title: 2018-10-14 Json을 Object타입으로 만들기
---

```
{
 "username":"jimmy",
 "channel":"test",
 "attachments":[
	{
	 "color":"#CC0000",
	 "title":"<http://www.naver.com|삼성전자> : 10.0",
			"fields":[
				{
					"title":"주가", "value":50000,"short":"true"
				},
				{
					"title":"변동률", "value":2.0,"short":"true"
				},
				{
					"title":"변동가격", "value":1000,"short":"true"
				}
				],
			"footer":"MADE BY JIMMY"
		}
	]
}
```
- 위의 형태를 처음에는 StockJsonDto로 만들었다. 하지만 메서드 내부적으로 복잡했기때문에 리팩토링을 하기로 했다.
- Field객체, Attachemnt객체, Converter객체로 나누었다.



```
public class Field extends LinkedHashMap<String, Object> {

    public Field(String title, Object value) {
        put("title", title);
        put("value", value);
        put("short", true);
    }
}
```
- Field객체에서는 title, value, sort라는 key값을 가지고 value값으로는 Stock객체에 있는 값들을 가지고 온다.
- Field객체에 HashMap을 상속을 받아서 Field객체가 위의 key값들을 가지고있다. key값들의 순서를 위해 LinkedHashMap을 상속을 받았다.

```
public class Attachment extends LinkedHashMap<String, Object> {
    private static final Logger logger =  LoggerFactory.getLogger(Attachment.class);
    public Attachment(Stock stock, String color) {
        put("color", color);
        put("title", "<" + stock.getDetailUrl() + " |" + stock.getName() + "> 주가 : " + stock.getCost() + " 변동률 : " + stock.getRate() + " 변동가격 : " + stock.getUpdn());
        put("text", "주가 : " + stock.getCost() + " 변동가 : " + stock.getUpdn());
    }
}

```
- Attachemnt객체에도 각 Filed의 순서가 필요하므로 LinkedHashMap을 상속을 받아서 Filed의 순서를 유지했고, color와 footer, title의 속성을 가지고 있다.

```
public class Converter extends LinkedHashMap<String, Object> {
    private static final Logger logger =  LoggerFactory.getLogger(Converter.class);
    private static final String red = "#CC0000";
    private static final String blue = "#0000FF";

    public Converter(Stock stock, String channel) {
        put("username", "jimmy");
        put("channel", channel);
        List<Attachment> attachments = new ArrayList<>();
        attachments.add(new Attachment(stock, checkColor(stock)));
        put("attachments", attachments);
    }

    public Converter(List<Stock> stocks, String channel) {
        put("username", "jimmy");
        put("channel", channel);
        List<Attachment> attachments = new ArrayList<>();
        for (Stock stock : stocks)
            attachments.add(new Attachment(stock, checkColor(stock)));
        put("attachments", attachments);
    }

    public static String checkColor(Stock stock) {
        if (stock.getRate() > 0.0)
            return red;
        return blue;
    }
}

```

- Converter객체에서는 Stock객체를 생성자를 통해 전달을 받는데, Stock객체의 변동률을 통해서 등락률을 알 수 있게 색상을 전달해준다.
- attachments라는 리스트를 생성을 해서, 그 안에 attchment를 추가한다.


#### 결론

- 위의 Json에서 각 계층별로 리팩토링을 진행을 했다. 이전에 했던 방법보다 훨씬 더 깔끔하고 유지보수가 쉬워졌다.
- Converter객체를 Gson.toJson(Converter)를 통해서 바로 Json형태로 변환을 했다.
