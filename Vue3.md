# VUE3 변경사항

### 바쁜 현대인을 요약

- Composition API라는게 들어오면서 데이터 선언, 함수 선언, 상태 관리 등이 조금 변함
- 컴포넌트 내에서 데이터, 함수, 상태를 호출하는 방식도 변함
- 없던 종류의 컴포넌트가 몇몇개 생길 예정 (문법은 미확정)
- 내부적으로 성능 향상을 위한 변화도 진행 예정

### 목차

Vue3에서 일어난 변화로써 유의미한 것(이야기할 것들)들의 리스트는 아래와 같습니다.

- 템플릿 생성 방식의 변화
- data, method 작성 방식의 변화
- Lifecycle hook 호출의 변화
- computed 속성 사용방법의 변화
- Composition API
- props와 this 바인딩의 분리
- emit과 this 바인딩의 분리
- Suspended Component
- Fragment
- Portal
- 내부적인 변화

### 템플릿 생성 방식의 변화

Vue2의 템플릿은 하나의 루트 엘리먼트만 허용하였습니다. 하지만 Vue3부터는 여러 개의 루트 엘리먼트를 갖는 컴포넌트(Fragment)를 지원합니다. 이를 이용하여 일부 경우에 불필요한 wrapper div가 필요한 경우를 제거하기 위해 사용될 수 있습니다. 불필요한 wrapper를 제거할 수 있는 경우의 예시로써, 라벨이 있는 input 박스를 생각해볼 수 있습니다.

```html
    <template>
    	<div>
    		<div class="input-label">
    			{{inputLabel}}
    		</div>
    		<input type="text"/>
    	</div>
    </template>
```

이 경우, 루트에 있는 `<div>` 태그가 아무 기능도 수행하지 않음에도 불구하고 기존 Vue2에서는 루트 엘리먼트를 하나로 유지하기 위해 유지하여야 했지만, Vue3에서는 이를 선택할 수 있습니다.

이러한 변화가 여러 개의 루트 엘리먼트를 사용하는 것을 권장하는 것은 아닙니다. 일반적인 BEM 규칙 하에서 원활한 스타일링을 위하여 컴포넌트를 래핑하는 스타일 엘리먼트를 배치하는 것을 권장합니다.

템플릿에서 데이터를 호출하는 방식도 조금씩 변경되었습니다. 기존에는 props나 method 등을 구분없이 이름만으로 호출할 수 있도록 동작하였는데, 이제는 문맥상으로 이런 것들을 조금 분리할 수 있게 되었습니다. 루트 엘리먼트에 관한 예제에서 `inputLabel` 이라는 변수(prop인지 data인지 function인지 몰랐던 무언가)를 호출하는 방식은 아래와 같이 변화합니다.

```html
    <template>
    	<div>
    		<div class="input-label">
    			<!--{{inputLabel}}-->
    			{{state.inputLabe}}
    		</div>
    		<input type="text"/>
    	</div>
    </template>
```

호출 방식의 변화는 사실 템플릿의 변화라기보다는 props, data, method를 다루는 방식에서의 변화와 밀접하게 연관되어 있습니다. 이에 대해서 조금 더 자세히 다뤄 보도록 하겠습니다.

### data, method 작성 방식의 변화

Vue3으로 넘어오면서 생긴 변화 중, 코드 측면에서 가장 큰 변화가 아닐까 하는 부분입니다. 기존의 data, method 등의 선언이 전부 setup이라는 메소드 안으로 편입되었습니다. 예시를 보는 것이 가장 이해하기 좋을 것 같습니다. 우선, 기존 Vue2에서 data와 method를 선언하는 방식은 아래와 같았습니다.

```javascript
    // Vue2.x
    export default {
      props: {
        title: String
      },
      data () {
        return {
          username: '',
          password: ''
        }
      },
      methods: {
        login () {
          // login method
        }
      }
    }
```

Vue3에서 바뀐 data 및 method 선언 방식은 아래와 같습니다.

```javascript
    // Vue 3.x
    export default {
      props: {
        title: String
      },
      setup () {
        const state = reactive({
          username: '',
          password: ''
        })
    
        const login = () => {
          // login method
        }
        return { 
          login,
          state
        }
      }
    }
```

기존 코드에서는 props, data, methods가 같은 계층에 존재했습니다. 하지만 Vue3에서는 props와 setup이 같은 계층에 존재하고, data는 state로, method 들은 각각의 기명함수로 작성되어 한번에 반환되도록 변화하였습니다.

state의 경우에도 그냥 선언하는 것이 아니라, vue reactive를 사용하게 되었습니다. Reactive(반응형)는 Vue가 반응형 시스템을 유지하기 위해서 사용하는 간단한 JavaScript 객체입니다. Reactive는 아래와 같이 동작합니다.
![img](https://media.vlpt.us/images/bluestragglr/post/50e4c7c7-c697-4c5a-8329-820828389777/Untitled.png)

([공식 문서](https://kr.vuejs.org/v2/guide/reactivity.html) 에서 가져온 이미지. 해당 페이지 번역에는 저도 조금 참여했습니다 🌱)

Vue2 에서는 data나 method, computed 등을 선언하게 되면 알아서 각각에 대해 위와 같이 동작하는 reactive 객체를 생성하였습니다. 하지만 그 과정이 묵시적이었고, 유저들은 위 과정을 알 필요가 없었습니다. 하지만 Vue3에서의 타입스크립트의 지원이나 state로의 명칭 변경, 명시적 reactive 사용 등을 보았을 때, 전반적으로 명료한 선언을 지향하는 방향으로 변화한 것으로 보입니다.

### Lifecycle hook 호출의 변화

Vue2의 lifecycle hook의 경우에 data, method와 같은 hierachy에서 선언하도록 되어 있었습니다. Vue3에서는 다른 변화들과 같이 lifecycle hook 또한 setup 내부에서 선언하도록 하였습니다. 기존에 존재하던 lifecycle hook의 종류는 거의 그대로 유지됩니다.

```javascript
    import { onBeforeMount, onMounted, onBeforeUpdate, onUpdated, onBeforeUnmount, onUnmounted, onActivated, onDeactivated, onErrorCaptured } from 'vue'
    
    export default {
      setup() {
        onBeforeMount(() => {
          // ... 
        })
        onMounted(() => {
          // ... 
        })
        onBeforeUpdate(() => {
          // ... 
        })
        onUpdated(() => {
          // ... 
        })
        onBeforeUnmount(() => {
          // ... 
        })
        onUnmounted(() => {
          // ... 
        })
        onActivated(() => {
          // ... 
        })
        onDeactivated(() => {
          // ... 
        })
        onErrorCaptured(() => {
          // ... 
        })
      }
    }
```

이 때, const 등으로 선언했던 state나 method들과는 달리 lifecycle hook은 return할 필요가 없습니다. 이는 작성 구조만 보아도 명료하게 알 수 있습니다. 각각의 함수는 실행되는 것이지 반환되는 값이나 함수 자체를 변수에 할당하는 것이 아니므로, 따로 return하여 setup의 return으로써 포함시킬 필요가 없습니다.

### Computed 속성 사용의 변화

computed 속성은 이제 별도 옵션이 아닌, state 선언문 내에 computed 속성에 대한 선언 구문을 추가하는 방식으로 변경됩니다. 마찬가지로 예제를 보면 이해가 쉽습니다. 기존의 computed 속성은 아래와 같이 작성하였습니다.

```javascript
    export default {
      // .. 
      computed: {
        lowerCaseUsername () {
          return this.username.toLowerCase()
        }
      }
    }
```

Vue 3부터는 아래와 같이 작성합니다.

```javascript
    import { reactive, computed } from 'vue'
    
    export default {
      props: {
        title: String
      },
      setup () {
        const state = reactive({
          username: '',
          password: '',
          lowerCaseUsername: computed(() => state.username.toLowerCase())
        })
    
        // ...
      }
```

이러한 변화에는 사용하는 것만 import하여 사용하도록 하고자 하는 철학이 담겨 있습니다. 기존의 컴포넌트 중 computed가 필요한 컴포넌트는 상당히 소수임에도 불구하고 모든 컴포넌트는 옵션으로써 computed를 작성하는 순간 별도의 import 없이 동작하였습니다. Vue 3에서는 이러한 변화를 수정하고자 하여 computed나 lifecycle hook 등 다양한 옵션들을 함수 형태로 동작하도록 변경하였습니다.

### Composition API

앞의 모든 변경의 핵심이 되는 것이 새로 등장한 Composition API입니다. 직역하자면 '구성 API' 정도가 되는 이 API는 컴포넌트를 선언하고 조립하기 위한 새로운 API입니다. 완전히 종료된 논의가 아닌, 어느 정도 이야깃거리가 남아있는 이슈지만 전반적으로 논리적이고 가독성이 좋은 코드를 작성하기 위해서 새로운 방식의 API를 도입하는 것에 동의하는 분위기입니다. (#42, Evan You가 작성한 https://github.com/vuejs/rfcs/pull/42 PR의 스레드에서 굉장히 많은 논의가 오갔습니다.)

Composition API가 등장하게 된 배경은 아래의 그림을 보면 이해하기 쉽습니다. 기존에는 볼륨이 큰 컴포넌트가 생겼을 때, 상태 변수(data에 선언된 변수들 등)의 선언, 이 변수들의 computed 메소드 바인딩, methods 함수 선언, lifecycle hook 선언 등이 혼재되어 논리에서 어긋난 구조를 생성하고 있었습니다.
![img](https://media.vlpt.us/images/bluestragglr/post/04f6bd1b-cf8b-40a2-97c8-f2ea41741225/Untitled%201.png)

기존의 이러한 구조를 가독성이 좋고 코드 안에 논리를 보존하는 방향으로 변화하고자 Composition API를 도입하게 되었습니다.

조금 더 구체적으로 Composition API가 동작하는 방식을 설명해 보겠습니다. 기존의 Vue는 Options API라는 것을 사용하고 있습니다. data, computed, methods 등을 사용할 수 있는 API입니다.

Options API는 굉장히 직관적이고 많은 사람들이 좋아하는 부분이었지만, (앞서 언급한 것처럼) 컴포넌트의 크기가 커졌을 때 유지보수가 굉장히 어려워진다는 문제를 야기했습니다. 하나의 feature에 대한 코드가 여기저기로 흩어졌죠. Composition API는 이러한 유지보수 측면에서의 문제를 해결하기 위한 해법으로 생각할 수 있습니다.

아래의 예제를 바탕으로 Composition API에 대해 이해해 봅시다.

```javascript
    import { reactive, computed } from 'vue'
    
    export default {
      props: {
        title: String
      },
      setup () {
        const state = reactive({
          username: '',
          password: '',
          lowerCaseUsername: computed(() => state.username.toLowerCase())
        })
    
    		const changeName = (name) => {
    			state.username = name
    		}
      }
```

코드를 좀 더 자세히, 하나씩 읽어 봅시다.

```javascript
    import { reactive, computed } from 'vue'
```

Composition API는 Vue의 핵심 기능들을 전부 import하는 대신 직접 선택하여 import할 수 있도록 해줍니다. 사용하기 위한 핵심 기능을 우선 import합니다.

```javascript
    export default {
       setup() {
```

setup 메소드는 Composition API의 핵심입니다. 단어 그대로 컴포넌트의 기능들을 조립(compose)하는 역할을 합니다. setup 안에 (앞의 예시들처럼) lifecycle hook을 걸거나 반응형 데이터 바인딩, computed 사용 등을 할 수 있습니다. 마지막에는 꼭 return해 주어야 하구요!

```javascript
    const state = reactive({
          username: '',
          password: '',
          lowerCaseUsername: computed(() => state.username.toLowerCase())
        })
```

컴포넌트 내부에서 사용하기 위한 상태에 대한 값으로써 data를 선언하는 대신 state를 선언하고 (이름은 바뀔 수 있습니다.) reactive object로 초기화합니다. computed 속성도 함께 작성합니다.

```javascript
    const changeName = (name) => {
    	state.username = name
    }
```

함수도 위와 같이 선언합니다. 이제 state 등을 호출할 때 this 바인딩이 필요하지 않습니다.

```javascript
    return { 
    	state, 
    	changeName
    };
```

마지막에 setup의 return에 포함시켜 줍니다.

조금 더 자세한 실습을 원한다면 https://learnvue.co/2019/12/a-first-look-at-vue3-a-vue-composition-api-tutorial/ 에 있는 과정을 따라해 보는걸 추천합니다!

### props와 this 바인딩의 분리

props에 접근하는 방법도 조금 변경되었습니다. 기존에 props 뿐 아니라 method, data 등을 전부 this에 바인딩함으로써 코드의 가독성이 떨어지는 것을 개선하기 위한 변화가 아닌가 하는 생각이 듭니다. 기존에는 아래와 같이 this의 하위 요소로써 prop을 직접 호출하였습니다.

```javascript
    mounted () {
        console.log('title: ' + this.title)
    }
```

위의 코드만으론 `this.title` 이 props의 값인지, data인지, method인지 구별할 수 없습니다. 변경된 문법은 아래와 같습니다.

```javascript
    setup (props) {
        // ...
        onMounted(() => {
          console.log('title: ' + props.title)
        })
        // ...
    }
```

위에서 소개한 것 처럼 mounted 라이프사이클 훅은 setup 안에 포함되며, setup은 props를 사용하기 위해서 props를 attribute로 받아야 합니다. 사용하는 것만 불러오도록 하는 관점과도 밀접하게 연관이 있다고 할 수 있습니다.

### emit과 this 바인딩의 분리

emit도 props와 마찬가지로 this에 더이상 바인드되지 않습니다. props와 같이, setup 함수의 attribute로써 호출할 수 있습니다. 기존 코드는 아래와 같았습니다.

```javascript
    login () {
          this.$emit('login', {
            username: this.username,
            password: this.password
          })
     }
```

새로 변경된 문법에서는 아래와 같이 호출합니다.

```javascript
    setup (props, { emit }) {
        // ...
    
        const login = () => {
          emit('login', {
            username: state.username,
            password: state.password
          })
        }
    
        // ...
    }
```

커스텀 이벤트 활용 등 이외의 활용방법은 여전히 동일합니다.

### Suspended Component

*여기부터는 코드를 직접 구동해 보지 않았습니다! 정확한 사용법 등에 차이가 있을 수 있습니다.

> (2020. 5. 27 추가) 아래에 등장하는 코드들은 알파 버전에서 "예상했던" 형태의 코드이며, 현재 릴리즈된 베타 버전에서는 정식 문법이 추가 된 것으로 보입니다. 아래의 코드들은 컨셉으로만 이해해 주시면 될 것 같습니다.

Suspended component는 기존에 리액트에서 지원하던 컴포넌트 종류중의 하나입니다. 컴포넌트 내에 있는 async 구문이 완료되지 않았을 때, fallback template를 대신 표시하도록 할 수 있습니다. 문법은 아래와 같습니다.

```html
    <Suspense>
      <template #default>
        <UserProfile />
        <FunnyCats /> 
      </template>
      <template #fallback>
        <div>Loading...</div>
      </template>
    </Suspense>
```

`#default` 와 `#fallback` 으로 항목을 구분하며, 여러 개의 async component가 존재하는 경우 모든 async가 종료되었을 때 fallback이 해제됩니다.

### Fragment

Vue3에서는 루트 엘리먼트가 여러 개인 컴포넌트 또한 지원합니다. 기존에는 `table` 안에 들어가는 `td` 두 개를 하나의 컴포넌트로 묶어 사용하는 것 등이 불가능했습니다. 특별한 문법은 없으며, 아래와 같이 그냥 기존 컴포넌트 생성하듯 생성하되 `<Fragment>`로 감싸 사용하면 됩니다.

```html
    <Fragment>
            <td>Hello</td>
            <td>World</td>
    </Fragment>
```

### Portal

리액트에서 사용하는 포탈 컴포넌트도 지원할 예정이라고 합니다. 포탈 컴포넌트는 내부 컨텐츠를 포탈 타겟 컴포넌트로 옮기는 컴포넌트입니다. 다만 아직 Vue에서의 정확한 문법은 결정되거나 공개되지 않았으며, 아래와 같이 동작할 것으로 ***"예상"\*** 하고 있습니다.

```html
    <template>
      <div>
        <div>
          <p>
            The content below this paragraph is
            rendered in the right/bottom (red) container by PortalVue
          </p>
    			
    			<!-- [중요] 아직 결정된게 아니에요! -->
          <Portal to="right-basic">
            <p class="red">
              This is content from the left/top container (green).
              The cool part is, it works across components,
              so you can send your content anywhere!
            </p>
          </Portal>
        </div>
        <PortalTarget name="right-basic"></PortalTarget>
      </div>
    </template>
```

예상 렌더링 결과는 다음과 같습니다.
![img](https://media.vlpt.us/images/bluestragglr/post/c46522f5-c631-4eb5-bc8b-6db1b5213dcc/Untitled%202.png)

### 기타 내부적인 변화

내부적으로도 구조에 변화가 있습니다. 여러가지 변화가 있겠지만, 주요한 변화로써 두 가지 정도를 꼽아볼 수 있을 것 같습니다.

- 렌더링 트리 최적화

기존 V-DOM의 설계는 Vue의 옵저버 기반 프록시를 유지하기 위해 상당히 많은 자원을 소모하고 있었다고 합니다. 모든 Dependency를 V-DOM tree에서 recursive하게 탐색했기 때문인데, `v-if`와 `v-for` directive 의 dependency tracking을 위해서 필요했던 기능이지만 대부분의 구문들이 static data를 다룬다는 점에서 불필요한 자원 소모라고 판단한 것 같습니다.

이를 해결하기 위에서 Vue는 dynamic/static component를 구분하기로 했습니다. 아래의 코드 예시를 먼저 봅시다.
![img](https://media.vlpt.us/images/bluestragglr/post/1589dec1-c126-40fe-9f92-f2400464c5fd/Untitled%203.png)

해당 코드의 연두색 박스 중 노란 박스 영역을 제외한 영역은 완전히 static한 코드입니다. 노란 박스 내에도 `message` 를 제외하면 정적인 코드만 존재합니다. 뷰는 이를 아래와 같이 묶습니다.
![img](https://media.vlpt.us/images/bluestragglr/post/b960174b-3cd0-4382-b36f-eb3a1ae3ab74/Untitled%204.png)

즉, 완전히 static한 코드가 되는 단위로 컴포넌트를 묶습니다. 노드는 static 컴포넌트와 dynamic 컴포넌트를 구분하게 되며, dynamic 코드 블럭이 영향을 받아 다시 렌더링할 때 static 컴포넌트에 접근하지 않아도 되므로, runtime을 줄일 수 있을 것으로 기대하고 있습니다.