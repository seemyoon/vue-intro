<template>
  <div>
    <h2>Post's page</h2>
    <my-input
        v-model="searchQuery"
        placeholder="Search"
    />
    <div class="app__btns">
      <my-button @click="showDialog">Create post</my-button>
      <my-select v-model="selectedSort" :options="sortOptions"/>
    </div>
    <my-button @click="fetchPosts">Get posts</my-button>
    <my-dialog v-model:show="dialogVisible">
      <post-form
          @create="createPost"/>
    </my-dialog>
    <post-list
        :posts="sortedAndSearchedPosts"
        @remove="removePost"
        v-if="!isPostsLoading"
    />
    <div v-else>Loading..</div>
    <div ref="observer" class="observer"></div>
    <!--    <div class="page__wrapper">-->
    <!--      <div-->
    <!--          v-for="pageNumber in totalPages"-->
    <!--          :key="pageNumber"-->
    <!--          class='pages'-->
    <!--          :class="{-->
    <!--            'current-pages': pages === pageNumber-->
    <!--          }"-->
    <!--          @click="changePage(pageNumber)">-->
    <!--        {{ pageNumber }}-->
    <!--      </div>-->
    <!--    </div>-->
  </div>
</template>

<script>
import PostForm from '@/components/PostForm.vue'
import PostList from '@/components/PostList.vue'
import MyDialog from "@/components/UI/MyDialog.vue";
import MyButton from "@/components/UI/MyButton.vue";
import axios from "axios";
import MySelect from "@/components/UI/MySelect.vue";
import MyInput from "@/components/UI/MyInput.vue";

export default {
  components: {MyInput, MySelect, MyButton, MyDialog, PostForm, PostList},
  data() {
    return {
      posts: [],
      dialogVisible: false,
      isPostsLoading: false,
      selectedSort: '',
      searchQuery: '',
      page: 1,
      limit: 10,
      totalPages: 0,
      sortOptions: [
        {value: 'title', name: 'By name'},
        {value: 'body', name: 'By content'}
      ]
    }
  },
  methods: {
    createPost(post) {
      this.posts.push(post)
      this.dialogVisible = false
    },
    removePost(postToRemove) {
      this.posts = this.posts.filter(post => post.id !== postToRemove.id)
    },
    showDialog() {
      this.dialogVisible = true
    },
    // changePage(pageNumber) {
    //   this.pages = pageNumber
    //   this.fetchPosts()
    // },
    async fetchPosts() {
      try {
        this.isPostsLoading = true;
        const res = await axios.get('https://jsonplaceholder.typicode.com/posts', {
          params: {
            _page: this.page,
            _limit: this.limit
          }
        })
        this.totalPages = Math.ceil(res.headers['x-total-count'] / this.limit)
        console.log(this.totalPages)
        this.posts = res.data
      } catch (e) {
        console.error(e)
      } finally {
        this.isPostsLoading = false
      }
    },
    async loadMorePosts() {
      try {
        this.page += 1;
        // this.isPostsLoading = true;
        const res = await axios.get('https://jsonplaceholder.typicode.com/posts', {
          params: {
            _page: this.page,
            _limit: this.limit
          }
        })
        this.totalPages = Math.ceil(res.headers['x-total-count'] / this.limit)
        console.log(this.totalPages)
        this.posts = [...this.posts, ...res.data]
      } catch (e) {
        console.error(e)
      }
      // finally {
      //   this.isPostsLoading = false
      // }
    }
  },
  mounted() {
    this.fetchPosts()
    // this.$refs.observer // <div ref="observer" class="observer"></div>
    const options = {
      rootMargin: '0px',
      threshold: 1.0
    }

    const cb = (entries, observer) => {
      if (entries[0].isIntersecting && this.page < this.totalPages) this.loadMorePosts()
    }
    const observer = new IntersectionObserver(cb, options)
    observer.observe(this.$refs.observer)
  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]))
    },
    sortedAndSearchedPosts() {
      return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
    }
  },
  watch: {
    //   selectedSort(newValue) {
    //     // console.log(newValue)
    //     this.posts.sort((post1, post2) => {
    //       return post1[newValue]?.localeCompare(post2[newValue])
    //     }) //this.selectedSort - are title or body. we compare title or body
    //     //newValue ===this.selectedSort
    //   },
  }
}
</script>

<style>


.app__btns {
  margin: 15px 0;
  display: flex;
  justify-content: space-between;
}

.observer {
  height: 30px;
  background: green;
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
  border: 2px solid teal;
}
</style>