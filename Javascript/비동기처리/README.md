# 자바스크립트 비동기 

### 1. 비동기 처리란?
  특정 코드의 연산이 끝날 때까지 코드의 실행을 멈추지 않고 다음 코드를 실행하는 자바스크립트의 특성을 의미함
  

---
### 2. 비동기 예제
#### 1. setTimeout
```js
console.log("a")
setTimeout(function() {
  console.log("b")
}, 1000)
console.log("c");
```
- 위 예제는 실행하면 a -> c -> b 순으로 출력된다.


#### 2. jQuery.ajax
```js 
function getData() {
  var useData;
  $.get('url', function(res) {
    useData = res;
  })
  return useData;
}

console.log(getData());
```
- 위 예제를 실행하면 undefined가 출력된다.


### 3. 콜백 함수로 비동기 처리 방식의 문제점 해결하기
```js
function getData() {
  $.get('url', function(res) {
    callBack(res);
  })
}
```
- 콜백 함수를 사용하면 데이터가 준비된 시점에 원하는 동작을 수행할 수 있음


### 4. 콜백 지옥
- 콜백 지옥은 콜백 함수를 연속적으로 사용할 때 발생하는 문제입니다.
```js
$.get('url', function(response) {
	parseValue(response, function(id) {
		auth(id, function(result) {
			display(result, function(text) {
				console.log(text);
			});
		});
	});
});
```
![비동기1](https://user-images.githubusercontent.com/58321856/113505240-7a92fe80-9578-11eb-8ca0-d94994b8f5d6.png)


### 5. Promise
- Promise 객체를 이용해서 비동기 작업이 완료된 후의 결과를 받고 이후 처리를 할 수 있다.
```js
function getData(callback) {
  return new Promise(function(resolve, reject) {
    $.get('url', function(res) {
      if(res) resolve(res)
    })
    reject(new Error("Fail!"));
  })
}

getData().then(function(result) {
  console.log(data)
}).catch(function(err) {
  console.log(err)
})
```


### 6. Asnyc / Await
- 비동기 처리 패턴의 최신 문법
```js
async function 함수명() {
  await 비동기 처리 메서드 명();
}
```
```js
function getData() {
  return fetch('url').then(res) {
    return res
  })
}
async function viewData() {
  var result = await getData()
  console.log(result)
}
```

- 예외 처리
```js
async function viewData() {
  try {
    var result = await getData()
    console.log(result)
  } catch(error) {
    console.log(error)
  }
}
```
