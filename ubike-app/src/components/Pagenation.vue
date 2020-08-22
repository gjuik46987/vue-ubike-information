<template>
  <div class="page-content">
    <nav v-if="pagerEnd > 0">
      <ul class="pagination">
        <li @click.prevent="setPage(currentPage - 1)" class="page-item">
          <a class="page-link" href>Previous</a>
        </li>

        <li
          v-for="i in pagerEnd"
          :class="{ active: i + pagerAddAmount === currentPage }"
          :key="i"
          @click.prevent="setPage(i + pagerAddAmount)"
          class="page-item"
        >
          <a class="page-link" href>{{ i + pagerAddAmount }}</a>
        </li>

        <li @click.prevent="setPage(currentPage + 1)" class="page-item">
          <a class="page-link" href>Next</a>
        </li>
      </ul>
    </nav>
  </div>
</template>
<script>
export default {
  name: "Pagenation",
  props: ["pagerEnd", "currentPage", "pagerAddAmount"],
  data() {
    return {};
  },
  methods: {
    setPage(val) {
      this.$emit("set-page", val);
    },
  },
};
</script>
<style scoped>
.page-content{
   margin-top:1rem;
   width:100%;
   display: flex;
   justify-content: center;
}
</style>

