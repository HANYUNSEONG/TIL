# Side Effect

컴퓨터 공학에서의 Side Effect에 대해서 정리하자

먼저 Side Effect를 검색하면 구글에서는 사전적 정의를 부작용이라고 한다.
컴퓨터 공학에서의 Side Effect는 아래와 같다.

> 함수가 결과값이 아닌 다른 상태를 변경할 때 Side Effect가 발생했다고 한다.

ISO/IEC 14882에서 Side Effect라는 용어를 다음과 같이 정의했다.

> Accessing an object designated by a volatile lvalue, modifying an object, calling a library I/O function, or calling a function that does any of those operations are all side effects, which are changes in the state of the execution environment.
>
> _ISO(국제표준화기구) / IEC(국제전기기술위원회)_

객체의 변경, 라이브러리 I/O 함수 호출, 변경에 관련한 함수를 호출하는 경우 등  
실행 환경의 상태가 변경되는 행위를 모두 사이드 이펙트가 발생했다고 볼 수 있다.

사실 사이드 이펙트라는 용어는 **관점, 사용하는 곳에 따라 다양한 의미를 지닐 수 있다.**  
하지만 일반적으로 사이드 이펙트는 위에서 설명한 것과 같다.  
한마디로 위에 설명을 정리해보았다. **"의도하지 않은 결과, 예상치 못한 효과가 발생한다."**
