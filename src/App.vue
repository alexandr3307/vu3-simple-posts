<template>
  <div class="app">
    <PostForm @create="createPost" />
    <PostList v-bind:posts="posts" />
  </div>
</template>

<script>
import PostList from "@/components/PostList";
import PostForm from "@/components/PostForm";
export default {
  name: "App",
  components: { PostForm, PostList },
  data() {
    return {
      posts: [
        { id: 1, title: "java", body: "Описание" },
        { id: 2, title: "java", body: "Описание" },
        { id: 3, title: "java", body: "Описание" },
      ],
    };
  },
  methods: {
    createPost(post) {
      this.posts.push(post)
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.app {
  padding: 20px;
}
</style>
