<template>
  <table class="table table-striped">
    <thead>
      <tr>
        <th @click="setSort('sno')">
          #
          <span v-show="currentSort === 'sno'">
            <i class="fa" :class="isSortDesc ? 'fa-sort-desc' : 'fa-sort-asc'" aria-hidden="true"></i>
          </span>
        </th>
        <th>場站名稱</th>
        <th>場站區域</th>
        <th @click="setSort('sbi')" class="pointer">
          目前可用車輛
          <span v-show="currentSort === 'sbi'">
            <i class="fa" :class="isSortDesc ? 'fa-sort-desc' : 'fa-sort-asc'" aria-hidden="true"></i>
          </span>
        </th>
        <th @click="setSort('tot')" class="pointer">
          總停車格
          <span v-show="currentSort === 'tot'">
            <i class="fa" :class="isSortDesc ? 'fa-sort-desc' : 'fa-sort-asc'" aria-hidden="true"></i>
          </span>
        </th>
        <th>資料更新時間</th>
      </tr>
    </thead>
    <slot></slot>
  </table>
</template>
<script>


export default {
  name: "MainTable",
  props:['current-sort','isSortDesc'],
  data() {
    return {
     
    };
  },
  created(){
      console.log(this.currentSort);
  },
  methods: {
    setSort(val){
      this.$emit('set-sort',val)
    },

  },
  computed: {
  } 
};
</script>