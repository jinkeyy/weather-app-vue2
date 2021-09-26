<template>
  <nav class="navbar navbar-light bg-light">
    <div class="container-fluid">
      <a class="navbar-brand">Dự Báo Thời Tiết 7 Ngày</a>
    </div>
  </nav>
</template>

<script>
export default {
  name: "Header",
  data() {
    const inputSearch = ""
    return {
      inputSearch
    }
  },
  methods: {
    search(){
      console.log(this.inputSearch);
    }
  },
};
</script>

<style>
</style>