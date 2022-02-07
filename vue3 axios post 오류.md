```vue
axios({
        method :'post',
        url : `${SERVER_URL}/camp/rate`,
        data : JSON.stringify({contentId : '10',userNo:20,campRateContent : 2,campRatePrice : 4, campRateCleanliness:3}),
        headers: {
                'Accept': 'application/json',
                'Content-Type': 'application/json'
              },
      })
      .then(res => {
        console.log(res)
      })
      .catch(err => {
        console.log(err)
      })
```

안되고 있음 해결법 아래에 넣기