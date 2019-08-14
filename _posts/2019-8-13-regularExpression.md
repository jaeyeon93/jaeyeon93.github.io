---
layout: post
title: 2019-8-13 JS 정규표현식
---

## 도입배경

- google html parser를 만들면서 정규표현식에 관한 필요성을 느끼게 되었다.

```javascript
service.email = getEmailId(response);
  service.name = $('#gamma > div > div:nth-child(2) > div > div:nth-child(6) > table:nth-child(5) > tbody > tr:nth-child(2) > td:nth-child(1) > span > span').text().trim();
  service.date = $('#gamma > div > div:nth-child(2) > div > div:nth-child(5)').text().split('주문 날짜:')[1].trim();
  service.renewal = $('#gamma > div > div:nth-child(2) > div > div:nth-child(6) > table:nth-child(5) > tbody > tr:nth-child(3) > td:nth-child(1)').text().split(':')[1].trim();
  console.log(service);

//result
{ email: 'jimmyjaeyeon@gmail.com',
  name: '1개월 이용권 (왓챠플레이)',
  date: '2019. 8. 9 오후 2시 51분 28초',
  renewal: '2019. 9. 9.' }

// parsing전 데이터
1개월 이용권 (왓챠플레이)
주문 번호:  GPA.3303-9400-7953-78210

주문 날짜:  2019. 8. 9 오후 2시 51분 28초
월간 구독 ‐ 자동 갱신 날짜: 2019. 9. 9.

```

- cheerio를 이용해서 html을 parsing하고, 그 안에서 메서드들을 남발해서 파싱을 하게되었다. 좀 더 깔끔하게 하기 위해 정규표현식을 도입해보려고 한다.

## 정규표현식

![](https://poiemaweb.com/img/regular_expression.png)

```javascript
const test_name = `1개월 이용권 (왓챠플레이)`;
const test_date = `주문 번호:  GPA.3303-9400-7953-78210

주문 날짜:  2019. 8. 9 오후 2시 51분 28초`;
const test_duration = `월간 구독 ‐ 자동 갱신 날짜: 2019. 9. 9.`;


```

- test_name에서 왓챠플레이를 추출
- test_date에서 2019. 8. 9를 추출
- test_duration에서 2019. 9. 9를 추출하고자 한다.

```javascript
const test_date = `주문 번호:  GPA.3303-9400-7953-78210

주문 날짜:  2019. 8. 9 오후 2시 51분 28초`;

const test_date2 = `주문 번호:  GPA.3303-9400-7953-78210

주문 날짜:  2019. 12. 12 오후 2시 51분 28초`;

const date_reg = /\d{4}\.\s\d{1,2}\.\s\d{1,2}/g;
// console.log(date_reg.exec(test_date));
console.log(date_reg.exec(test_date2)[0]);
```



- 정규표현식을 이용해서 2019. 8. 9라는 문자와 2019. 12. 12라는 문자를 추출했다.
- date_reg에 보면 \d{4}는 4자리수를 이야기를 한다. 2019다음에 '.'과 ' '으로 구분이 되어있어서 \d{4}\.\s로 구분을 했다. 여기서 주의할 점은 정규표현식에서 .은 모든문자를 의미하기 때문에 '.'을 사용하고 싶으면 \\. 을 써야한다. 처음에 그냥 '.'으로 했다가 고생을 했다. d{1,2}의 경우 1-9월일수도 있고 10-12월 일수도 있기때문에 저렇게 구분을 했다. 뒤에 day에 관한 부분도 1-9일과 10-31일까지의 경우가 있기때문에 저렇게 표현을 했다.

```javascript
const fromEmail = 'Google Play <googleplay-noreply@google.com>';
const emailReg = /<(.*?)>/;
console.log(emailReg.exec(email));
//result
[ '<googleplay-noreply@google.com>',
  'googleplay-noreply@google.com',
  index: 12,
  input: 'Google Play <googleplay-noreply@google.com>',
  groups: undefined ]

```

- email이 누구로부터 왔는지를 확인하기 위해 response.json에서 From value를 가지고 왔다.
- From value에는 위의 변수와 같이 문자열이 담겨있고, <>사이에 있는 내역이 필요해서 정규표현식을 적용했다.
- 정규표현식 <>사이에 (.*?)는  match everything in a non-greedy way and capture it.라는 의미를 가진다. 결과는 위의 결과로 나오고 result[1]을 통해 값을 가지고 올 수 있다.
