#### 에러 상황

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





#### 해결법 1

```javascript
const send = () => {
      console.log(campRateDto.value)
      axios({
        method : 'POST',
        url : `${SERVER_URL}/camp/rate?campRateCleanliness=${campRateDto.value.campRateCleanliness}&campRateContent=${campRateDto.value.campRateContent}&campRateFacility=${campRateDto.value.campRateFacility}&campRatePrice=${campRateDto.value.campRatePrice}&campRateTitle=${campRateDto.value.campRateTitle}&contentId=10&userNo=${campRateDto.value.userNo}`,
                // http://localhost:8080/camp/rate?campRateCleanliness=5&campRateContent=sdaf&campRateFacility=5&campRatePrice=5&campRateTitle=%EC%98%A4%EC%9E%89%EC%98%A4%EC%9E%89%EB%98%90%EC%9E%89&contentId=10&userNo=25
        headers : {'content-type' : 'application/json'}, 
        data : {}
      })
      .then(res => {
        console.log(res)
      })
      .catch(err => {
        console.log(err)
      })
    }
```



#### 해결법 2

```javascript
const send = () => {
      console.log(campRateDto.value)
      let form = new FormData()
      form.append("campRateCleanliness",campRateDto.value.campRateCleanliness)
      form.append("campRatePrice",campRateDto.value.campRatePrice)
      form.append("campRateFacility",campRateDto.value.campRateFacility)
      form.append("userNo",campRateDto.value.userNo)
      form.append("campRateContent",campRateDto.value.campRateContent)
      form.append("campRateTitle",campRateDto.value.campRateTitle)
      form.append("contentId",campRateDto.value.contentId)
      axios({
        method : 'POST',
        url : `${SERVER_URL}/camp/rate`,
        headers : {'content-type' : 'application/json'}, 
        data : form
      })
      .then(res => {
        console.log(res)
      })
      .catch(err => {
        console.log(err)
      })
    }
```

