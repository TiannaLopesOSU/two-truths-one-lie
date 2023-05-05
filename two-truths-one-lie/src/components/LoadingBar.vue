<template>
  <div class="container">
    <div class="loading-circle m-2"></div>
    <h3 class="m-3">Loading ...</h3>
  </div>
</template>
<script>
export default {
  name: "LoadingBar",
  data() {
    return {};
  },
  computed: {},
};
</script>
<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.loading-circle {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  border: 5px solid #ccc;
  border-top-color: #00bcd4;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
</style>
