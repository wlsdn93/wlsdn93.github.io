---
title : "[minimal-mistakes] 공유기능 커스텀하기"
excerpt : "Copy URL 버튼을 추가해보자"
author_profile: true
sidebar:
  nav: "sidebar"
category: Blog
---

**`minimal-mistakes`** 테마는 **`Twitter`**, **`Facebook`**, **`LinkedIn`** 과 같은 SNS에 글을 공유하는 기능을 기본으로 제공해주고 있다.  

## **공유기능 활성화**  
**`minimal-mistakes`** 테마에서 기본적으로 제공해주는 공유 기능은 **`_config.yml`** 을 통해 활성화 할 수 있다.    
```yaml
# Defaults
defaults:
  # _posts
  - scope:
      path: ""
      type: posts
    values:
      layout: single
      author_profile: true
      read_time: false
      comments: true
      share: true  # 공유기능 활성화
      related: true
```
  
공유하기 기능을 활성화 해주면, 아래와 같이 포스트 최하단에 Twitter, Facebook, LinkedIn 버튼들이 만들어진다.  
![image.png](/assets/images/blog-images/social-share.png)  
   
내 기준에서는 세 가지 버튼들 보다는, **`Copy URL`** 버튼 하나만 사용하고 싶었기 때문에 간단한 커스텀을 해보았다.  

## **공유버튼 커스텀**  
내게 필요한 버튼을 추가하기 위해선 **`_include/social_share.html`** 파일을 수정해야 한다.  
불필요한 버튼들을 주석처리 하고, 아래 코드를 추가해주자.  
```html
  <a class="btn btn--copyUrl"
     onclick="navigator.clipboard.writeText(window.location.href.toString())">
  Copy URL
  </a>
``` 
커스텀 클래스 **`(class="btn btn--copyUrl")`** 를 위해 **`assets/css/main.scss`** 파일에 스타일을 추가해주자.  
```scss
.btn--copyUrl {
  background-color: #27dbc2;
  color: #fff
}
.btn--copyUrl:hover {
  background-color: #11999e;
  color: #fff
}
```