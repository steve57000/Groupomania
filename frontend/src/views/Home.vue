<template>
  <div class="home">
    <div class="container">
      <Post />
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Post from "@/components/post.vue";

export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: "Home",
  components: {
    Post,
  },
};
</script>

<style lang="scss" scoped>
@import "../assets/css/user.scss";
</style>
