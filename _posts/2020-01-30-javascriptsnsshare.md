---
title: "Javascript SNS(Social Media) 공유 방법 (How to share social media in javascript.)"
date: 2020-01-17
categories: posts
tags: ["git"]
---

## | 문제
프로젝트 개발 중 페이지를 SNS (Social Media) 로 공유하는 기능을 추가해야 했다.

공유할 SNS는 Facebook, Twitter, Kakao, 그리고 간단하게 링크를 클립보드로 복사하는 정도이다.

## | 과정
간단하다.

Facebook 이나 Twitter 의 경우 공유 API 가 존재하기 때문에, 해당 API 로 파라미터만 넘겨주면 된다.

반면 카카오의 경우에는 카카오 개발자 페이지에서 앱(or 웹) 등록을 해주어야 한다.

공유할때 그냥 링크만 달랑 공유할 수 있지만 그렇기에는 밋밋하므로 미리보기 페이지가 같은게 나오면 좋다.

미리보기 페이지는 Facebook 에서 만든 OpenGraph 가 있다. 사실상 표준으로 굳어지는 듯 하다.

오픈그래프는 간단하게 메타태그에 Prefix 로 "og" 가 붙은 태그를 말하며, 페이지를 크롤링 하는 봇들이 해당 태그를 읽어서 미리보기로 만들어주는 것이다.

오픈그래프를 설정할 때는 다음과 같이 중요한 점이 있다.

- 외부에 공개된 도메인 또는 고정 아이피로 된 페이지어야 한다.
- Javascript 로 동적으로 변경되는 오픈 그래프는 적용되지 않는다. 처음 불러온 html 페이지의 og 속성만을 가져온다.
- 동적 메타태그를 생성하기 위해서는 서버사이드 렌더링 또는 아파치 nginx 와 같은 웹서버에서 따로 Redirect 를 해주어야 한다.
- og:image 의 경우 "http://" 를 포함한 절대 경로를 입력하며 상대 경로를 입력하면 안된다.
- og:image 의 경우 최소 크기가 200x200 픽셀이다.
- Twitter 전용 Open Graph 가 있지만 Facebook 의 OpenGraph 도 공용으로 사용된다.

## | 결론
```typescript
// Facebook 공유
window.open(
  'https://www.facebook.com/sharer/sharer.php?u=' + encodeURIComponent('공유할 주소')
);
// Twitter 공유
window.open(
  'https://twitter.com/intent/tweet?url=' + encodeURIComponent('공유할 주소')
);
// Kakao 공유
Kakao.init('카카오 개발자 페이지에서 발급받은 키');
Kakao.Link.sendDefault({
  objectType: 'feed',
  content: { 
    title: '공유 미리보기 제목', 
    imageUrl: '공유 미리보기 썸네일', 
    link: { webUrl: '공유할 주소' } 
  },
  buttons: [{ 
    title: '링크 열기', link: { webUrl: '공유할 주소' } 
  }];
});
```

```html
<!-- Facebook (Common) Open Graph -->
<meta property="og:type" content="미리보기 종류" />
<meta property="og:url" content="페이지 URL" />
<meta property="og:title" content="미리보기 제목" />
<meta property="og:description" content="미리보기 설명" />
<meta property="og:image" content="미리보기 이미지 주소" />
<!-- Twitter Open Graph -->
<meta name="twitter:card" content="트위터 미리보기 종류" />
<meta name="twitter:url" content="페이지 URL" />
<meta name="twitter:title" content="미리보기 제목" />
<meta name="twitter:description" content="미리보기 설명" />
<meta name="twitter:image" content="미리보기 이미지 주소" />
```