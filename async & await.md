# async & await

```javascript
async test() { 
      
      const response = await axios.post(`${SERVER_URL}/mate`,this.partyData)
      if (this.mateImageUrl) { 
        const res2 = await axios.post(`${SERVER_URL}/mate/${response.data.dto.mateNo}`,this.mateImageUrl)
        console.log(res2)
        this.$store.dispatch("viewMate", this.partyData);
          }
        this.$router.push({ name: "Mateparty" })


      }
```

async await를 통해 비동기를 표현 하면 get을 한번더 불러올 필요가 없게 된다. 진작 알았더라면 더 많은 부분에 요긴하게 사용했을텐데... promise 와 async 모두 마스터할 수 있도록 해야할 듯.

```javascript
test() {
      
      console.log(this.partyData)
      console.log(2)
      axios({
        method: "post",
        url : `${SERVER_URL}/mate`,
        data : this.partyData,
      })
      .then((res) => {
          // 이미지삽입
          console.log(res.data.dto.mateNo);
          this.mateChatNm = res.data.dto.mateNo;
          console.log(this.mateImageUrl);
          if (this.mateImageUrl) {
            axios({
              method: "post",
              headers: { "Content-Type": "multipart/form-data" },
              url: `${SERVER_URL}/mate/${res.data.dto.mateNo}`,

              data: this.mateImageUrl,
            })
              .then((res) => {
                console.log(res);
                const viewFunc = (data) => {
                  // console.log(data)
                    this.$store.dispatch("viewMate", data);
              
                  };
                  axios({
                    methods: "get",
                    url: `${SERVER_URL}/mate`,
                  })
                  .then((res) => {
                    // console.log(res.data.list)
                    viewFunc(res.data.list);
              
                  })

                  .catch((err) => {
                    console.log(err);
                  });
              })
              .catch((err) => {
                console.log(err);
              });
          }


          

          setTimeout(() => {
            this.$router.push({ name: "Mateparty" });
          }, 3000);
```

