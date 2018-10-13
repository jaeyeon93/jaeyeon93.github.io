---
layout: post
title: 2018-10-13 JsonMapping
---

## 자바객체를 이용해서 Json매핑하기

- 슬랙에서 메세지를 보내기 위해서는 알맞은 형태의 Json타입으로 전송을 해야된다.
- 주식객체를 가지고 Json을 메핑을 했다.

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
- 위와 같은 형태로 변환을 해야한다.

```
public class StockJsonDto {
    private static final Logger logger =  LoggerFactory.getLogger(StockJsonDto.class);
    private Stock stock;

    public StockJsonDto(Stock stock) {
        this.stock = stock;
    }

    public JsonObject makeJson() {
        JsonObject object = new JsonObject();
        object.addProperty("username", "jimmyBot");
        object.addProperty("channel","test");
        object.add("attachments", attachements());
        return object;
    }

    public JsonArray attachements() {
        JsonArray attachments = new JsonArray();
        JsonObject attach = new JsonObject();
        attach.addProperty("color", "#CC0000");
        attach.addProperty("title", "<" + getStock().getDetailUrl() + " |" + getStock().getName() + ">");
        attach.add("fields", makeFileds());
        attach.addProperty("footer", "MADE BY JIMMY");
        attachments.add(attach);
        return attachments;
    }

    public JsonArray makeFileds() {
        String [] titles = {"주가", "변동률", "변동가격"};
        Double [] values = {(double)getStock().getCost(), getStock().getRate(), (double)getStock().getUpdn()};
        JsonArray fields = new JsonArray();
        for (int i = 0; i < titles.length; i++) {
            JsonObject field = new JsonObject();
            field.addProperty("title", titles[i]);
            field.addProperty("value", values[i]);
            field.addProperty("short", true);
            fields.add(field);
        }
        return fields;
    }

    public Stock getStock() {
        return stock;
    }
}
```

- StockJsonDto에서 Stock객체를 받으면 그것을 바탕으로 위와같은 Json타입으로 변환을 해준다.
- Json의 value타입으로 Array가 있어서 문제해결에 이슈가 있었는데, 이 부분에 대한것을 Gson의 JsonArray를 이용을 해서 해결을 했다.

#### 느낀점
- 뭔가 깔끔하게 설계를 하고 싶은데, 많이 찜찜하다.
