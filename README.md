# 항해 플러스 프론트엔드 4기 과제 2주차 <br/>: Chapter 1-2. 프레임워크 없이 SPA 만들기
- [과제 체크포인트](#과제-체크포인트)
  - [기본과제](#기본과제)
  - [심화과제](#심화과제)
- [과제 셀프회고](#과제-셀프회고)
  - [기술적 성장](#기술적-성장)
  - [코드 품질](#코드-품질)
  - [학습 효과 분석](#학습-효과-분석)

## 과제 체크포인트

### 기본과제

#### 1) 가상돔을 기반으로 렌더링하기
- [x] createVNode 함수를 이용하여 vNode를 만든다.
- [x] normalizeVNode 함수를 이용하여 vNode를 정규화한다.
- [x] createElement 함수를 이용하여 vNode를 실제 DOM으로 만든다.
- [x] 결과적으로, JSX를 실제 DOM으로 변환할 수 있도록 만들었다.

#### 2) 이벤트 위임
- [x] 노드를 생성할 때 이벤트를 직접 등록하는게 아니라 이벤트 위임 방식으로 등록해야 한다
- [x] 동적으로 추가된 요소에도 이벤트가 정상적으로 작동해야 한다
- [x] 이벤트 핸들러가 제거되면 더 이상 호출되지 않아야 한다

### 심화과제

#### 1) Diff 알고리즘 구현
- [x] 초기 렌더링이 올바르게 수행되어야 한다
- [x] diff 알고리즘을 통해 변경된 부분만 업데이트해야 한다
- [x] 새로운 요소를 추가하고 불필요한 요소를 제거해야 한다
- [x] 요소의 속성만 변경되었을 때 요소를 재사용해야 한다
- [x] 요소의 타입이 변경되었을 때 새로운 요소를 생성해야 한다

#### 2) 포스트 추가/좋아요 기능 구현
- [x] 비사용자는 포스트 작성 폼이 보이지 않는다
- [x] 비사용자는 포스트에 좋아요를 클릭할 경우, 경고 메세지가 발생한다.
- [x] 사용자는 포스트 작성 폼이 보인다.
- [x] 사용자는 포스트를 추가할 수 있다.
- [x] 사용자는 포스트에 좋아요를 클릭할 경우, 좋아요가 토글된다.

## 과제 셀프회고

### 기술적 성장

> ✍️ 새로 학습한 개념

#### VirtualDOM

VirtualDOM은 React의 핵심 개념 중 하나로 메모리 상에 존재하는 DOM의 가벼운 복사본입니다.<br/>
React는 상태 변경 시 새로운 VirtualDOM 트리를 생성하고, 이전 트리와 비교하여 변경사항을 파악합니다.<br/>
이 과정을 통해 실제 DOM 조작을 최소화하여 성능을 개선합니다.

#### VirtualDOM의 구조

가상 돔은 실제 돔의 형태를 본따 만든 객체 덩어리입니다.

- 실제 돔 구조
```html
<div id="app">
  <ul>
    <li>
      <input type="checkbox" class="toggle" />
      todo list item 1
      <button class="remove">삭제</button>
    </li>
    <li class="completed">
      <input type="checkbox" class="toggle" checked />
      todo list item 2
      <button class="remove">삭제</button>
    </li>
  </ul>
  <form>
    <input type="text" />
    <button type="submit">추가</button>
  </form>
</div>
```

- 가상 돔 구조
```javascript
function virtualDom(type, props, ...children) {
  return { type, props, children: children.flat() }
}

virtualDom('div', { id: 'app' },
  virtualDom('ul', null,
    virtualDom('li', null,
      virtualDom('input', { type: 'checkbox', className: 'toggle' }),
      'todo list item 1',
      virtualDom('button', { className: 'remove' }, '삭제')
    ),
    virtualDom('li', { className: 'completed' },
      virtualDom('input', { type: 'checkbox', className: 'toggle', checked: true }),
      'todo list item 2',
      virtualDom('button', { className: 'remove' }, '삭제')
    ),
  ),
  virtualDom('form',
    virtualDom('input', { type: 'text' }),
    virtualDom('button', { type: 'submit' }, '추가'),
  )
);
```

> 🤩 기존 지식의 재발견/심화

#### diff 알고리즘

diff는 두 개의 파일 또는 문서 간의 차이점을 식별하는 알고리즘 또는 도구를 의미합니다.<br/>
원본과 수정된 버전 사이의 변경 내용을 탐지하고 추가된 내용, 제거된 내용, 수정된 내용 등을 식별합니다.

#### diff 알고리즘 과정

1. 이전 Virtual DOM 트리와 새 Virtual DOM 트리를 비교
2. 루트 노드에서 시작하여 이전과 새로운 노드를 비교
3. 두 노드가 다른 유형이면 새 노드를 만들어 기존 노드를 대체
4. 두 노드가 같은 유형이면 속성을 비교하여 변경된 것이 있는지 확인
  4-1. 변경된 속성이 없으면 바로 사용
  4-2. 변경된 속성이 있으면 해당 속성을 업데이트
5. 자식 노드를 재귀적으로 비교

> 🥳 구현 과정에서의 기술적 도전과 해결

테스트 코드 중 "이벤트 핸들러가 제거되면 더 이상 호출되지 않는다." 라는 항목에서 계속해서 에러가 발생했었습니다.<br/>
`eventManager`에 `removeEvent` 부분에 문제가 지속해서 발생하는 것 같아 수정에 수정을 거듭했습니다.

처음에는 `eventListeners`에서 특정 `element`를 키로 하여 이벤트와 핸들러를 관리하고,<br/>
수정한 코드에서는 `eventListeners`에서 `eventType`을 키로 사용하여 이벤트 타입 별로 요소와 핸들러를 관리하도록 변경했습니다.

처음 코드에서 `eventListeners`가 `element`를 중심으로 관리되었기 때문에,<br/>
동일한 `eventType`을 여러 `element`을 사용하거나 삭제할 때 잘못된 핸들러를 삭제하거나 충돌이 발생할 수 있었습니다.<br/>
이로 인해 `eventType` 중심으로 설계를 변경하여 문제 해결을 할 수 있었습니다.

- AS-IS
```javascript
const eventListeners = new Map();

export function removeEvent(element, eventType, handler) { 
   const elementEvents = eventListeners.get(element); 
   if (!elementEvents) return; 

   if (handler) { 
     const handlers = elementEvents.get(eventType); 
     if (handlers) { 
       handlers.delete(handler); 
       if (handlers.size === 0) { 
         elementEvents.delete(eventType); 
       } 
     }
   } else { 
     elementEvents.delete(eventType); 
   } 

   if (elementEvents.size === 0) { 
     eventListeners.delete(element); 
   }
```

- TO-BE
```javascript
const eventListeners = new Map();
export function removeEvent(element, eventType, handler) {
  const handlersMap = eventListeners.get(eventType);
  if (!handlersMap) return;

  const handlers = handlersMap.get(element);
  if (!handlers) return;

  if (handler) {
    handlers.delete(handler);

    if (handlers.size === 0) {
      handlersMap.delete(element);
    }
  } else {
    handlersMap.delete(element);
  }

  if (handlersMap.size === 0) {
    eventListeners.delete(eventType);
  }
}
```

### 코드 품질

> 👍 특히 만족스러운 구현

`updateElement` 부분에서 diff 알고리즘을 사용하여 변경된 속성이나 태그를 업데이트하는 과정을 구현한 부분입니다.<br/>
먼저 어떤 로직을 만들어야하는지 정의하고, 하나씩 코드를 구성하여 내용을 정리했습니다. 

```javascript
export function updateElement(parentElement, newNode, oldNode, index = 0) {
  const existingNode = parentElement?.childNodes[index];

  // oldNode만 있는 경우: oldNode를 parentElement에서 제거한다.
  if (!newNode && oldNode) {
    parentElement.removeChild(existingNode);
    return;
  }

  // newNode만 있는 경우: newNode를 parentElement에 추가한다.
  if (!oldNode && newNode) {
    parentElement.appendChild(createElement(newNode));
    return;
  }

  // oldNode와 newNode 모두 string인 경우: oldNode와 newNode 내용이 다르다면, newNode 내용으로 교체한다.
  if (typeof oldNode === "string" || typeof newNode === "string") {
    if (newNode !== oldNode) {
      parentElement.replaceChild(createElement(newNode), existingNode);
    }
    return;
  }

  // oldeNode와 newNode의 태그 이름(type)이 다를 경우: oldNode를 제거하고 해당 위치에 newNode를 추가한다.
  if (oldNode.type !== newNode.type) {
    parentElement.replaceChild(createElement(newNode), existingNode);
    return;
  }

  // oldNode와 newNode의 태그 이름(type)이 같을 경우: newNode와 oldNode의 속성을 비교하여 변경된 부분만 반영한다.
  updateAttributes(existingNode, newNode.props || {}, oldNode.props || {});

  // oldNode와 newNode를 순회하며, 앞에 조건식을 반복한다.
  const newChildren = newNode.children || [];
  const oldChildren = oldNode.children || [];

  const maxLength = Math.max(newChildren.length, oldChildren.length);

  for (let i = 0; i < maxLength; i++) {
    updateElement(existingNode, newChildren[i], oldChildren[i], i);
  }
}
```

### 학습 효과 분석

> ❗ 가장 큰 배움이 있었던 부분

#### VirtualDOM을 구현하는 과정

1. `createVNode` : 실제 돔을 가상 돔으로 만들기
2. `normalizeVNode` : 가상 노도를 표준화된 형태로 변환, 다양한 타입을 처리하여 일관된 형식의 가상노드를 반환하는 과정
3. `createElement` : 가상 돔을 실제 돔으로 변환
4. `updateElement` : 모든 태그 내용들을 비교하여 변경된 부분에 대한 수정/추가/삭제
5. `eventManager` : 렌더링 된 돔에 이벤트를 등록
6. `renderElement` : 상태가 바뀐 돔을 렌더링

> ❓ 추가 학습이 필요한 영역

#### Map과 WeakMap의 차이점

- Map
  - `key`로 모든 값을 사용할 수 있습니다.
  - 숫자, 문자열, 객체 등 어떠한 데이터 타입도 `key`가 될 수 있습니다.
  - 강한 참조를 가지므로 `delete`를 호출하지 않는 한 key-value 쌍은 메모리에 유지됩니다.
  - `for..of`, `map` 메서드를 통해 반복이 가능합니다.
  - `size` 프로퍼티 key-value 쌍의 개수 확인이 가능합니다.
  - 일반적으로 key-value 데이터를 저장하고 반복, 크기 확인이 필요한 경우에 사용됩니다.

- WeakMap
  - `key`는 반드시 객체여야만 합니다.
  - 문자열이나 숫자와 같은 기본 자료형은 `key`로 사용할 수 없습니다.
  - 약한 참조를 가지므로 다른 곳에서 참조되지 않으면 가비지 컬렉션의 대상이 됩니다.
  - `size` 프로퍼티가 없습니다.
  - 반복할 수가 없어 저장된 키나 값을 직접 열거하거나 확인할 수 없습니다.
  - 주로 DOM 요소의 메타데이터를 저장하거나 비공개 데이터를 객체와 연관시키는 데 유용합니다.

> 👊 실무 적용 가능성

당장 실무에 적용하기 보다는 React의 Virtual DOM의 개념과 작동 방식을 조금이나마 이해할 수 있어서 <br/>
React를 좀 더 잘 활용하고 사용할 수 있는 계기가 된 것 같습니다.
