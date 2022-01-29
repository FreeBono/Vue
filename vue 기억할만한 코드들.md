# vue 기억할만한 코드들



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

