# 이벤트 버블링과 캡처링

## 1. 버블링
<img width="457" alt="event-bubble" src="https://user-images.githubusercontent.com/58321856/113536924-38b69680-9612-11eb-9e2d-5ae5c4ed728f.png">


### 버블링의 원리
- 한 요소에 이벤트가 발생하면, 이 요소에 할당된 핸들러가 동작하고, 이어서 부모 요소의 핸들러가 동작합니다. 가장 최상단의 조상 요소를 만날 때까지 이 과정이 반복되면서 요소 각각에 할당된 핸들러가 동작합니다.
- 예시 코드
```html
<form onClick="alert('form')">
  form
  <div onClick="alert('div')">
    div
    <p onClick="alert('p')">p</p>
  </div>
</form>
```
1. p 요소를 클릭하면 p에 있는 onClick 핸들러가 동작합니다.
2. 그 위에 요소인 div에 onClick 핸들러가 동작합니다.
3. 상위 요소인 form에 onClick 핸들러가 동작합니다.
4. 계속하여 상위로 올라가면서 각 요소에 있는 onClick 핸들러가 동작합니다.

- 이렇게 동작을 하기 때문에 p 요소를 클릭하면 p -> div -> form 순서로 alert가 표시됩니다.
- 이런 흐름을 **이벤트 버블링** 이라고 합니다.


### event.target
- 이벤트가 발생한 요소는 **target** 요소라고 부릅니다.
- **tihs**는 실행 중인 핸들러가 할당된 요소입니다.
- 예시 코드
```html
<form>
  form
  <div>
    div
    <p>p</p>
  </div>
</form>

<script>
  document.querySelector('form').addEventListener('click', function(event) {
    alert(`target => ${event.target.tagName}, this => ${this.tagName}`)
  })<img width="457" alt="event-bubble" src="https://user-images.githubusercontent.com/58321856/113536971-54ba3800-9612-11eb-8b26-63a3ae568f49.png">![Uploading event-bubble.png…]()


</script>
```
1. form 안에 어떤 요소를 클릭해도 alert가 나옵니다.
2. alert에 **target은 클릭한 요소**가 나오고 **this는 핸들러가 할당된 요소인 form**이 나오게 됩니다.


### 버블링 중단
- 핸들러에 이벤트를 처리하고 상위 요소로 안 올라가게 중단하도록 할 수 있습니다.
```html
<div onCLick="alert('div')">
  <p onClick="event.stopPropagation()">p</p>
</div>
```
- p 태그를 클릭해도 상위 요소에 있는 onClick 핸들러는 실행되지 않습니다.
- 이유는 이벤트 객체의 메소드인 event.stopProagatio을 사용했기 때문입니다.


### stopPropagation vs preventDefault
- 두 개의 차이는 preventDefault는 고유의 동작을 중단시키고 stopPropagation은 버블링을 막습니다.

---


## 2. 캡쳐링
<img width="459" alt="event-capture" src="https://user-images.githubusercontent.com/58321856/113536996-669bdb00-9612-11eb-953d-4a9ff0abf15c.png">


### 캡쳐링이란?
- 이벤트가 하위 요소로 전개되는 것
> 캡쳐링 단계를 이용하는 경우는 흔치 않고 캡쳐링에 관한 코드를 발견할 일은 거의 없을거라고 합니다.
```html
<div>
  div
  <p>p</p>
</div>
<script>
  for(let elem of document.querySelectorAll('*')) {
      elem.addEventListener("click", e => alert(elem.tagName), true);
  }
</script>
```
1. 위 코드에서 addEventListener에 3번째 요소를 true로 하면 캡처링을 사용할 수 있다.
2. p 요소를 클릭하면 버블링이었다면 p -> div ... 이렇게 나와야한다.
3. 캡처링은 위에서 아래 순서이기 때문에 ... div -> p 이렇게 출력이 된다.


## 🙌 참고한 사이트
- [코어 자바스크립트 / 버블링과 캡쳐링](https://ko.javascript.info/bubbling-and-capturing)
- [캡틴판교 / 이벤트 버블링, 이벤트 캡처 그리고 이벤트 위임까지](https://joshua1988.github.io/web-development/javascript/event-propagation-delegation/#%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EC%BA%A1%EC%B3%90---event-capture).
