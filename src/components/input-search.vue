<template>
  <div class="input-group">
    <input
      type="search"
      class="form-control rounded"
      v-bind:placeholder="placeholder"
      aria-label="Search"
      aria-describedby="search-addon"
      v-model="inputText"
    />
    <button type="button" v-on:click="onClick" class="btn btn-outline-primary">Tìm kiếm</button>
  </div>
</template>

<script>
export default {
  name: "InputSearch",
  props: {
    placeholder: String,
  },
  data() {
      const inputText = ""
      return {
          inputText
      }
  },
  methods: {
      onClick(){
          let data = {
              inputText: this.inputText
          }
          this.$emit("onChange",data)
      }
  },
};
</script>

<style scoped>
.input-group {
  padding: 10px;
}
</style>