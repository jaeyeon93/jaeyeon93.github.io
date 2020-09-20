---
layout: post
title: 2020-9-20 NextJS 서버사이드 렌더링
comments: true
---

## 배경
- NextJS는 react와 달리 SSR(Server-Side Rendering)을 지원한다. 그렇다면 서버사이드 렌더링이란 무엇일까? 일반적으로 리액트의 경우, 브라우저에서 화면에 필요한 데이터들을 axios를 활용하여 요청하고. 브라우저가? Js가 화면을 그리게된다. 하지만 NextJS의 경우 NextJS서버가 요청을 미리보내서 프론트에서 렌더링하기전에 미리 응답해준다. 이를통해 SEO에 좀 더 최적화를 할 수 있다.


```javascript
export async function getServerSideProps(context) {
  const cookies = parseCookies(context);
  const { unit_type_id } = context.query;
  let token = cookies["connect.sid"] || null;
  let isUser = true;
  let userInfo = {};
  let errorCode = null;
  let data = {};
  try {
    if (isEmpty(token)) {
      isUser = false;
    } else {
      const res = await getMyApt(token);
      userInfo = res.data.message;
    }
    const [
      apartPrice,
      apartHistory,
      apartNearBy,
      apartStandardPrice,
      apartComplex,
      apartRegulation
    ] = await axios.all([
      getApartGraph(unit_type_id),
      getApartHistory(unit_type_id),
      getApartNearBy(unit_type_id),
      getApartStandardPrice(unit_type_id),
      getApartComplex(unit_type_id),
      getApartRegulation(unit_type_id)
    ]);
    data["apartPrice"] = apartPrice.data;
    data["apartHistory"] = apartHistory.data;
    data["apartNearBy"] = apartNearBy.data;
    data["apartStandardPrice"] = apartStandardPrice.data;
    data["apartComplex"] = apartComplex.data;
    data["apartRegulation"] = apartRegulation.data;
    data["unit_type_id"] = unit_type_id;
  } catch (error) {
    logError(error);
    errorCode = error.response?.status || 500;
  }
  return {
    props: {
      data,
      errorCode,
      initialState: {
        userState: {
          isUser,
          userInfo
        }
      }
    }
  };
}
```
- NextJs의 pages디렉토리 아래 파일에서 getServerSideProps라는 함수를 호출하면 해당 페이지가 호출되기전에 미리 요청을 보내서 응답을 받고. 해당 컴포넌트의 props로 전달해준다. 브라우저에서 요청을 보내는것과 SSR에서 요청보내는 주체는 다르기떄문에 주의해야한다. 이 부분은 다음 블로깅에서 다루도록 해야겠다.
