# 컴포넌트, 다시 생각하기

- 해당 글은 [컴포넌트, 다시 생각하기](https://youtu.be/HYgKBvLr49c) 영상을 보고 정리하는 차원에서 작성했습니다.

## 개요

최근에 컴포넌트를 개발하면서 많이 고민하게 만드는 것 중의 하나가 어떻게 컴포넌트를 분리할 것인가에 대해서 깊이 고민을 해보던 중 유튜브에서 좋은 영상을 발견해서 봤는데 너무 좋은 내용이어서 정리하게 되었다.

## 1. 바라보기

영상에서 케이크를 예로 들어서 의존성에 대해 설명하였다.

1. 케이크를 만드려면 `밀가루, 설탕, 계란`이 필요하다.
2. 케이크는 `밀가루, 설탕, 계란`에 **의존**한다.
3. 케이크의 **의존성**: `밀가루, 설탕, 계란`

이걸 React 컴포넌트에 적용해보자  
의존성을 생각하여 컴포넌트를 개발하면 조금 더 좋은 방향으로 개발할 수 있을 것 같다.

### React 컴포넌트의 의존성

컴포넌트의 의존성에 대해서 생각해보자.

1. 스타일
   - 컴포넌트의 스타일
2. 로직
   - UI 조작에 필요한 로직
3. 전역 상태
   - UI를 표현하기 위해서 사용자의 액션을 통해 변경되는 상태
4. 리모트 데이터 스키마
   - API 서버에서 내려주는 데이터의 모양

위에 4가지의 컴포넌트의 의존성 중에 유일하게 외부에 있는 의존성은 4번이다.

> 대다수의 제품은 **하나의 특정 동작**을 원합니다.  
> **로딩**을 표시하며 **필요한 데이터를 모두** 가져온 후  
> 가져온 데이터를 활용하여 **전체 뷰를 렌더링**한다.

컴포넌트의 새로운 기능을 추가할 때 기능에 맞는 Props를 추가하게 되는데 이걸 React 컴포넌트의 **숨은 의존성**이라고 발표자분이 말씀하셨다.

새로운 정보를 표현하기 위해서 1개의 컴포넌트가 아닌 컴포넌트를 사용하는 다른 중간에 있는 컴포넌트도 수정하게 된다. 이는 리모트 데이터가 가진 숨은 의존성이라고 한다.

Props가 아닌 전역 상태에 데이터를 저장한다고 가정해도 결국 루트 컴포넌트에 의존하게 된다.

## 2. 함께두기

### 1. 비슷한 관심사라면 가까운 곳에

위에서 보았던 의존성 중에서 함께 둘 수 있는 의존성을 생각해보자.  
스타일과 로직을 함께 둘 수 있을 것 같다.

코드로 한번 보자

```tsx
import React from 'react';
import styled from '@emotion/react';

const Something = () => {
  const { ... } = useHooksForSomething();

  return <Container>Hello, World!</Container>
}

function useHooksForSomething() {};

const Container = styled.div``;
```

로직과 스타일을 하나의 파일로 관리하고 있다. 간단한 컴포넌트의 경우 위와 같은 방법을 사용해도 괜찮지만 규모가 큰 컴포넌트의 경우 로직과 스타일을 다른 파일로 나누는 데 완전 다른 폴더에 두는 것이 아닌 같은 폴더 혹은 가까운 폴더에 두어 관리해서 집중력이 흐트러지지 않도록 한다.

#### 리모트 데이터

리모트 데이터가 내 컴포넌트까지 내려오는 과정을 살펴보자  
루트 컴포넌트 -> 다른 컴포넌트 -> 내 컴포넌트 과정을 거쳐서 내려온다. props를 통해서 데이터를 받게 되면  
내 컴포넌트는 루트 컴포넌트의 강한 의존성이 생긴다고 볼 수 있다.

props를 통해 데이터 전체를 받는 게 아닌 ID를 받아서 내 컴포넌트에서 받은 ID로 데이터를 찾아서 처리하면 루트 컴포넌트와 강한 의존성을 뗴어낼 수 있다.

먼저 props로 데이터 자체를 받는 걸 코드로 살펴보자

```tsx
import { IArticle } from "api";

interface Props {
  article: IArticle;
}

const Something: React.ExoticComponent<Props> = (props) => {
  return (
    <div>
      <h1>{props.article.title}</h1>
    </div>
  );
};
```

위 코드는 Article 데이터를 props로 받아 처리한다. 이걸 개선해보자

```tsx
import { useArticle } from "store";

interface Props {
  articleId: string;
}

const Something: React.ExoticComponent<Props> = (props) => {
  const article = useArticle(props.articleId);

  return (
    <div>
      <h1>{article.title}</h1>
    </div>
  );
};
```

이렇게 하면 다른 컴포넌트와의 의존성을 많이 줄일 수 있다.

솔직히 여기까지 보고 이걸 어떻게 적용할까에 대한 의문점이 너무 커졌다. 다음 챕터로 넘어가면서 의문점을 해결하자

## 3. 데이터를 ID 기반으로 정리하기

Normalization - 정규화

먼저 서버에서 데이터가 아래와 같이 넘어온다고 가정하자

```ts
{
  id: '123',
  author: {
    id: '1',
    name: 'Paul',
  },
  title: 'My awesome blog post',
  comments: [
    {
      id: '324',
      commenter: {
        id: '2',
        name: 'Nicole'
      }
    }
  ]
}
```

위 데이터에서 ID 기반으로 특정 객체에 접근하려고 하면 쉽지 않을 것 같다.  
계속 보자

```ts
{
  result: '123',
  entities: {
    articles: {
      '123': {
        id: '123',
        author: '1',
        title: 'My awesome blog post',
        comments: ['324'],
      },
    },
    users: {
      '1': { id: '1', name: 'Paul' },
      '2': { id: '2', name: 'Nicole' },
    },
    comments: {
      '324': { id: '324', commenter: '2' },
    },
  },
}
```

데이터를 정리했다. 이러면 모델명과 ID를 가지고 특정 데이터를 뽑을 수 있게 되었다.  
**이를 데이터 정규화 영어로는 Data Normalization이라고 한다.**

이런 데이터 정규화를 도와주는 라이브러리가 있다.  
[normalizr](https://www.npmjs.com/package/normalizr)  
살펴보면 좋을 것 같다.

이렇게 정규화된 데이터를 저장소에 두면 컴포넌트에서 ID만 가지고 데이터 저장소에 요청하여 특정 데이터를 쉽게 가져와서 렌더링할 수 있게 된다.

그런데 여기에는 또다른 숨은 의존성이 생기게 된다.  
바로 모델을 상위에서 정확하게 알고 있어야한다는 점이다.

이를 느슨하게 풀기 위해서 **Global ID**(전역 아이디)를 사용하여 특정 객체를 식별하기 위해 모델명을 넘길 필요 없이 ID 값을 가지고 특정 데이터를 식별할 수 있도록 하는 체계이다.

위에서 봤던 예제를 다시 보자

```tsx
import { useArticle } from "store";

interface Props {
  articleId: string;
}

const Something: React.ExoticComponent<Props> = (props) => {
  const article = useArticle(props.articleId);

  return (
    <div>
      <h1>{article.title}</h1>
    </div>
  );
};
```

`useArticle`이라는 걸 보면 해당 컴포넌트가 Article 데이터가 필요하다는 걸 컴포넌트 바깥에서 주입받고 있음을 확인할 수 있다.

이를 전역 ID를 통해 데이터를 가져오는 `useNode` hooks로 바꿔보자

```tsx
import { useNode } from "~/store";

interface Props {
  articleId: string;
}

const Something: React.ExoticComponent<Props> = (props) => {
  const article = useNode({ on: "Article" }, props.articleId);

  return (
    <div>
      <h1>{article.title}</h1>
    </div>
  );
};
```

사용할 모델 정보 마저 컴포넌트 내부에 함께둘 수 있게 되었다.  
여기서 더 나아가보자

GOI (Global Object Identification)  
GOI를 이용하여 사용할 데이터의 모델 정보를 바깥에서 주입받는 게 아니라 컴포넌트 내부에서 제어해서 컴포넌트의 의존성을 줄일 수 있다.

추가로 Refetch 작업에도 좋다.

## 4. 이름 짓기

> 의존한다면 그대로 드러내기

그대로 드러내면 의존하는 모델에 따라 컴포넌트를 분리할 수 있고 협업할 때 조금 더 좋을 것 같다.

## 5. 재사용하기

개발할 때 편리한 것보다 **변경점이 있을 떄 편리하게** 하는 것이 중요하다.  
컴포넌트에서 변화점이라고 하면 디자인이나 로직이 될 수 있지만 그 중 가장 영향이 큰 변화는 리모트 데이터 스키마다.  
실제로 최근 프로젝트 진행 시 특정 컴포넌트가 리모트 데이터 스키마에 크게 의존하고 있는 경우가 있었는데 그 데이터 스키마가 변경되자 많은 시간을 들여 리팩토링을 했던 경험이 있다.

> 같은 모델을 의존하는 컴포넌트 : 재사용  
> 다른 모델을 의존하는 컴포넌트 : 분리

## 6. 결론

- **비슷한 관심사라면 가까운 곳에**
- **데이터를 ID 기반으로 정리하기**
- **의존한다면 그대로 드러내기**
- **모델 기준으로 컴포넌트 분리하기**
