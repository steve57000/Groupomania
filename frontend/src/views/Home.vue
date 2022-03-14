<!-- eslint-disable -->
<template>
  <div class="home">
    <div class="container">
      <Post />
    </div>
  </div>
</template>
<!-- eslint-disable -->
<style lang="scss" scoped>
@import "../assets/css/user.scss";
</style>
<!-- eslint-disable -->
<script>
// @ is an alias to /src
import Post from "@/components/post.vue";

export default {
  name: "Home",
  components: {
    Post,
  },
};
</script>
