<template>
  <div class="app">
    <h1>Страница с постами</h1>
    <my-button style="margin: 15px 0" @click="showDialog"
      >Создать пост</my-button
    >
    <MyDialog v-model:show="dialogVisible">
      <PostForm @create="createPost"
    /></MyDialog>
    <PostList v-bind:posts="posts" @remove="removePost" />
  </div>
</template>

<script>
import PostList from "@/components/PostList";
import PostForm from "@/components/PostForm";
import MyDialog from "@/components/UI/MyDialog";
import MyButton from "@/components/UI/MyButton";
export default {
  name: "App",
  components: { MyButton, MyDialog, PostForm, PostList },
  data() {
    return {
      posts: [
        { id: 1, title: "java", body: "Описание" },
        { id: 2, title: "java", body: "Описание" },
        { id: 3, title: "java", body: "Описание" },
      ],
      dialogVisible: false,
    };
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
