<template>
  <div class="app">
    <h1>Страница с постами</h1>
    <my-button style="margin: 15px 0" @click="showDialog"
      >Создать пост</my-button
    >
    <MyDialog v-model:show="dialogVisible">
      <PostForm @create="createPost"
    /></MyDialog>
    <PostList
      v-if="!isPostsLoading"
      v-bind:posts="posts"
      @remove="removePost"
    />
    <div v-else>Идет загрузка</div>
  </div>
</template>

<script>
import PostList from "@/components/PostList";
import PostForm from "@/components/PostForm";
import MyDialog from "@/components/UI/MyDialog";
import MyButton from "@/components/UI/MyButton";
import axios from "axios";
export default {
  name: "App",
  components: { MyButton, MyDialog, PostForm, PostList },
  data() {
    return {
      posts: [],
      dialogVisible: false,
      isPostsLoading: false,
    };
  },
  mounted() {
    this.fetchPosts();
  },
  methods: {
    createPost(post) {
      this.posts.push(post);
      this.dialogVisible = false;
    },
    removePost(post) {
      this.posts = this.posts.filter((p) => p.id !== post.id);
    },
    showDialog() {
      this.dialogVisible = true;
    },
    async fetchPosts() {
      try {
        this.isPostsLoading = true;
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/posts?_limit=10"
        );
        this.posts = response.data;
      } catch (e) {
        console.log(e);
      } finally {
        this.isPostsLoading = false;
      }
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
