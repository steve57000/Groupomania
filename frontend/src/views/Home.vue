<template>
  <div class="home">
    <div class="container">
      <Post />
    </div>
  </div>
</template>

<style lang="scss" scoped>
.home {
  background: conic-gradient(
    at -30% 50%,
    rgba(59, 52, 52, 0.85) 18%,
    rgba(77, 75, 75, 0.5) 30%,
    rgba(20, 20, 20, 0.85) 40%
  );
}
.container {
  padding-top: 100px;
  padding-bottom: 100px;
}
</style>

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
