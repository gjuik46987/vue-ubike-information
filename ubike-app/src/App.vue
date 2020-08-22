<template>
  <div id="app">
      <h1>YouBike 臺北市公共自行車即時資訊</h1>
      <div class="input-column">
         <input type="text" v-model="searchText">  
      </div>
     <MainTable :current-sort="currentSort" :is-sort-desc="isSortDesc" >
        <tbody>
      <tr v-for="s in slicedUbikeStops" :key="s.sno">
        <td>{{ s.sno }}</td>
        <td>{{ s.sna }}</td>
        <td>{{ s.sarea }}</td>
        <td>{{ s.sbi }}</td>
        <td>{{ s.tot }}</td>
        <td>{{ timeFormat(s.mday) }}</td>
      </tr>
    </tbody>
    </MainTable>
   
      <Pagenation  
     :pager-end="pagerEnd" 
     :current-page="currentPage" 
     :pager-add-amount="pagerAddAmount"
     @set-page="setPage"
     /> 
    
  </div>
</template>

<script>
import MainTable from './components/MainTable';
import Pagenation from './components/Pagenation';

// 單頁顯示筆數
const COUNT_OF_PAGE = 10;
// 頁碼最大數量
const PAGINATION_MAX = 10;

export default {
  name: 'App',
  components: {
    MainTable,
    Pagenation
  },data(){
      return{
      ubikeStops: [],
      currentSort: "sno",
      isSortDesc: false,
      searchText: "",
      currentPage: 1,
      }
  },
  methods:{
  async query_get_api() {
      await fetch(
        "https://tcgbusfs.blob.core.windows.net/blobyoubike/YouBikeTP.gz"
      )
        .then((res) => res.json())
        .then((res) => {
          // 將 json 轉陣列後存入 this.ubikeStops
          this.ubikeStops = Object.keys(res.retVal).map(
            (key) => res.retVal[key]
          );
        });
    },
    timeFormat(val) {
      // 時間格式
      const pattern = /(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/;
      return val.replace(pattern, "$1/$2/$3 $4:$5:$6");
    },
    setPage(page) {
      // 設定目前頁數
      if (page < 1 || page > this.totalPageCount) {
        return;
      }
      this.currentPage = page;
    },
    setSort(sortType) {
      // 切換排序
      if (sortType === this.currentSort) {
        this.isSortDesc = !this.isSortDesc;
      } else {
        this.currentSort = sortType;
        this.isSortDesc = false;
      }
    },
  },
  computed:{
    filtedUbikeStops() {
      // 過濾搜尋
      return this.ubikeStops.length === 0
        ? []
        : this.ubikeStops.filter((d) => d.sna.includes(this.searchText));
    },
    sortedUbikeStops() {
      // 拿過濾的結果做排序
      const filtedStops = [...this.filtedUbikeStops];

      return this.isSortDesc
        ? filtedStops.sort((a, b) => b[this.currentSort] - a[this.currentSort])
        : filtedStops.sort((a, b) => a[this.currentSort] - b[this.currentSort]);
    },
    slicedUbikeStops() {
      // 將排序的結果做分頁切割
      const start = (this.currentPage - 1) * COUNT_OF_PAGE;
      const end =
        start + COUNT_OF_PAGE <= this.sortedUbikeStops.length
          ? start + COUNT_OF_PAGE
          : this.sortedUbikeStops.length;

      return this.sortedUbikeStops.slice(start, end);
    },
    totalPageCount() {
      // 計算總頁數
      return Math.ceil(this.filtedUbikeStops.length / COUNT_OF_PAGE);
    },
    pagerEnd() {
      // 頁碼尾端
      return this.totalPageCount <= PAGINATION_MAX
        ? this.totalPageCount
        : PAGINATION_MAX;
    },
    pagerAddAmount() {
      // 頁碼位移
      const tmp =
        this.totalPageCount <= PAGINATION_MAX
          ? 0
          : this.currentPage + 4 - this.pagerEnd;

      return tmp <= 0
        ? 0
        : this.totalPageCount - (PAGINATION_MAX + tmp) < 0
        ? this.totalPageCount - PAGINATION_MAX
        : tmp;
    },
  },
  created() {
    this.query_get_api();
  },
  
}
</script>

<style >
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.input-column{
   margin:1rem 0;
}
.page{
  width:100%;
  display: flex;
  justify-content:center;
}
</style>
