```vue
<template>
  <div>
    <input class="stage-search" type="text" v-model="search" placeholder="스테이지 검색" @input="handleSearchInput" @keydown.tab="KeydownTab" />


  </div>
</template>

<script>
export default {
  name : 'Searchbar',
  data() {
    return {
      stageInfoList : [{title : 'abc'},{title : 'bcd'},{title : 'cde'},{title : 'def'},{title : 'efg'},]
    }
    
  },
  methods : {
    handleSearchInput(e) { 
      this.search = e.target.value;
      // console.log(this.search)
    
      if(this.search.length !== 0) {
         clearTimeout(this.debounce);
         this.debounce = setTimeout(() => {
          const filteredList = this.stageInfoList.filter(item => item.title.includes(this.search));
      
          this.searchList = filteredList; }, 500);
        }
      else { 
        clearTimeout(this.debounce);
        this.debounce = setTimeout(() => {
           this.searchList = []; }, 500);
        }
    },
    


  }
}
</script>

<style>

</style>
```

