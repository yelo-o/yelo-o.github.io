---
layout: single
title:  "Clipboard"
categories: coding
tag: [javascript, chrome, IE11]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

## 복사 붙여넣기 그냥 하면 되는 것 아닌가요?
기본적으로 복사를 하게 되면 클립보드라는 곳에 저장이 됩니다. (setter)

그리고 붙여넣기 할 때 클립보드에 있는 데이터를 불러와서 출력하게 해주는 것이죠. (getter)

## 클립보드란 무엇인가요?
클립보드는 컴퓨터나 기타 디지털 장치에서 임시로 데이터를 저장하는 기능입니다. 

주로 복사, 잘라내기 및 붙여넣기 작업을 수행할 때 사용되며,

일반적으로 텍스트, 이미지, 파일 등의 데이터를 클립보드에 저장할 수 있습니다.

## 브라우저 환경에 따라 지원하는 API가 다른 Clipboard API
일반적으로는 사용자가 복사 붙여넣기를 할 때 추가적인 기능이 필요하지는 않지만,

복사한 데이터를 가공하는 경우 사용하는 경우가 있을 수 있겠습니다. 

예를 들면, 네이버 블로그에서 글을 복사해서 어딘가에 붙여넣기 할 때 보면 출처가 붙기도 합니다.

이런 경우가 클립보드를 가공해서 사용하는 경우라고 볼 수 있겠습니다.

이 때 Web에서 사용이 가능한 API는 크게 IE브라우저이냐 IE브라우저가 아니냐로 볼 수 있겠습니다.

요즘 누가 IE를 쓰냐고 생각할 수 있겠지만, 레거시 코드를 많이 보유하고 있는 기업들은

아직도 IE 브라우저를 쓰고 있습니다.

### IE11 브라우저

#### window.clipboardData

```javascript
const textToCopy = "복사할 텍스트";
window.clipboardData.setData("text", textToCopy); // 복사
window.clipboardData.getData("text"; // 붙여넣기
```

### IE11 이외의 브라우저 (크롬, 파이어폭스 등..)

방법이 두 가지로 가능한데 navigator를 사용하는 방법과, ClipboardEvent를 사용하는 방법입니다.

#### ClipboardEvent
ClipboardEvent에서 발생하는 이벤트 인자에는 clipboardData가 들어있기 때문에, 

특정 엘리먼트에 이벤트리스너를 등록해 발생하는 이벤트 인자를 통해 구현이 가능하다.

```javascript
const textToCopy = "복사할 텍스트";
event.clipboardData.setData("text", textToCopy); // 복사
event.clipboardData.getData("text"; // 붙여넣기
```

#### Clipboard API
document.execCommand()를 대체하기 위해 만들어지는 방식으로, 이벤트리스너를 추가하는 방식으로 구현이 가능하다.

다만, 붙여넣을 때 브라우저가 권한에 대해 최초에 1번 물어보고, document에 focus된 상태여야 사용이 가능하다.

- 텍스트 복사하기
  ```javascript
  const textToCopy = "복사할 텍스트";
  
  navigator.clipboard.writeText(textToCopy)
    .then(() => {
      console.log('텍스트가 성공적으로 복사되었습니다.');
    })
    .catch(err => {
      console.error('텍스트 복사에 실패했습니다:', err);
    });
  ```

- 텍스트 붙여넣기

  ```javascript
  navigator.clipboard.readText()
    .then(text => {
      console.log('클립보드에서 가져온 텍스트:', text);
    })
    .catch(err => {
      console.error('클립보드에서 텍스트를 읽어오는 데 실패했습니다:', err);
    });
  ```


