# Vuex란?



- 무수히 많은 컴포넌트의 데이터를 관리하기 위한 상태 관리 패턴이자 라이브러리
- React의 Flux 패턴에 기인





# Flux란?



- MVC패턴의 데이터흐름을 해결하는 개발 패턴
  1. action : 화면에서 발생하는 이벤트 또는 사용자 입력
  2. dispatcher : 데이터를 변경하는 방법(메서드)
  3. model : 화면에 표시할 데이터
  4. view : 사용자에게 비춰지는 화면





# Vuex 컨셉



- State : 컴포넌트 간에 공유하는 데이터  ( data() )
- View : 데이터를 표시하는 화면 ( template )
- Action : 사용자의 입력에 따라 데이터를 변경하는 ( methods )



# Vuex 구조



#### 컴포넌트 => 비동기 로직(Actions) => 동기 로직(Mutations) => 상태 (State)



- state : 여러 컴포넌트에 공유되는 데이터 (data)
- getters : 연산된 state 값을 접근하는 속성 (computed)
- mutations : state 값을 변경하는 이벤트 로직, 메서드 (methods)
  - state 값을 변경할 수 있는 유일한 메서드
  - commit() 으로 동작
  - 사용 이유  : state 접근위치를 확인하기 위해
- actions : 비동기 처리 로직을 선언하는 메서드 (aysnc methods)





