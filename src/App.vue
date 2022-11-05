<template>
  <div class="app">
    <h1>Страница с постами</h1>
    <my-input v-model="searchedQuery" placeholder="Поиск... " />
    <div class="app__buttons">
      <my-button @click="showDialog">Создать пост</my-button>
      <my-select v-model="selectedSort" :options="sortOptions"></my-select>
    </div>

    <MyDialog v-model:show="dialogVisible">
      <PostForm @create="createPost"
    /></MyDialog>
    <PostList
      v-if="!isPostsLoading"
      :posts="sortedAndSearchedPosts"
      @remove="removePost"
    />
    <div v-else>Идет загрузка</div>
    <div ref="observer" class="observer"></div>
    <!--    <div class="page__wrapper">-->
    <!--      <div-->
    <!--        v-for="pageNumber in totalPages"-->
    <!--        :key="pageNumber"-->
    <!--        class="page"-->
    <!--        :class="{ 'current-page': page === pageNumber }"-->
    <!--        @click="changePage(pageNumber)"-->
    <!--      >-->
    <!--        {{ pageNumber }}-->
    <!--      </div>-->
    <!--    </div>-->
  </div>
</template>

<script>
import PostList from "@/components/PostList";
import PostForm from "@/components/PostForm";
import MyDialog from "@/components/UI/MyDialog";
import MyButton from "@/components/UI/MyButton";
import axios from "axios";
import MySelect from "@/components/UI/MySelect";
import MyInput from "@/components/UI/MyInput";
export default {
  name: "App",
  components: { MyInput, MySelect, MyButton, MyDialog, PostForm, PostList },
  data() {
    return {
      posts: [],
      dialogVisible: false,
      isPostsLoading: false,
      selectedSort: "",
      searchedQuery: "",
      page: 1,
      limit: 10,
      totalPages: 0,
      sortOptions: [
        { value: "title", name: "По названию" },
        { value: "body", name: "По содержимому" },
      ],
    };
  },
  mounted() {
    this.fetchPosts();
    const options = {
      rootMargin: "0px",
      threshold: 1.0,
    };
    const callback = (entries, observer) => {
      if (entries[0].isIntersecting && this.page < this.totalPages) {
        this.loadMorePosts();
      }
    };
    const observer = new IntersectionObserver(callback, options);
    observer.observe(this.$refs.observer);
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
          "https://jsonplaceholder.typicode.com/posts",
          {
            params: {
              _page: this.page,
              _limit: this.limit,
            },
          }
        );
        this.totalPages = Math.ceil(
          response.headers["x-total-count"] / this.limit
        );
        this.posts = response.data;
      } catch (e) {
        console.log(e);
      } finally {
        this.isPostsLoading = false;
      }
    },
    async loadMorePosts() {
      this.page += 1;
      try {
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/posts",
          {
            params: {
              _page: this.page,
              _limit: this.limit,
            },
          }
        );
        this.totalPages = Math.ceil(
          response.headers["x-total-count"] / this.limit
        );
        this.posts = [...this.posts, ...response.data];
      } catch (e) {
        console.log(e);
      } finally {
      }
    },
    // changePage(pageNumber) {
    //   this.page = pageNumber;
    // },
  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) =>
        post1[this.selectedSort]?.localeCompare(post2[this.selectedSort], {
          numeric: true,
        })
      );
    },
    sortedAndSearchedPosts() {
      return this.sortedPosts.filter((post) =>
        post.title.toLowerCase().includes(this.searchedQuery.toLowerCase())
      );
    },
  },
  // watch: {
  //   page() {
  //     this.fetchPosts();
  //   },
  // },
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
.app__buttons {
  display: flex;
  margin: 15px 0;
  justify-content: space-between;
}
.page__wrapper {
  display: flex;
  margin-top: 15px;
}
.page {
  border: 1px solid black;
  padding: 10px;
}
.current-page {
  border: 2px solid green;
}
.observer {
  height: 30px;
  background: green;
}
</style>
