# vue 기억할만한 내용들



#### 필터링

```vue

if (filterlist.value.style) {
        if (filterlist.value.style.length === 1) {
          matelists.value = matelists.value.filter(function(item) {
            return item.mateCampstyle === filterlist.value.style[0];
            })
            
        } else if (filterlist.value.style.length === 2) {
          matelists.value = matelists.value.filter(function(item) {
            
            return item.mateCampstyle === filterlist.value.style[0] || item.mateCampstyle ===filterlist.value.style[1]
          })
        }
      } else if (filterlist.value.style.length === 3) {
          matelists.value = matelists.value.filter(function(item) {
            
            return item.mateCampstyle === filterlist.value.style[0] || item.mateCampstyle ===filterlist.value.style[1] || item.mateCampstyle ===filterlist.value.style[2]
          })
        }
```



#### setup 에서 연산 순서

```vue
//created 사이에 axios가 있을 때 created가 모두 처리 된 후에 axios가 시작됨.
그러므로 콘솔을 찍어도 값이 null로 나오게 되는 것을 주의

setup() {
    //   const store = useStore()
    //   const userNo = store.state.myNum
    const matchData = ref([])
    
    axios({
        method : 'get',
        url : `http://localhost:8080/mate/match/4`
    })
    .then(res => {
        const temp = res.data
        matchData.value = temp
        console.log(matchData.value)
    })
    .catch(err =>{
        console.log(err)
    })
    
    
    
      return {
        matchData,
      }
  }
```

