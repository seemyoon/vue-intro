<script>

export default {
  data() {
    return {
      post: {
        title: '',
        body: ''
      }
    }
  },
  methods: {
    createPost() {
      this.post.id = Date.now();
      this.$emit('create', this.post)
      this.post = {
        title: '',
        body: '',
      }
    }
  }
}
</script>

<template>
  <form @submit.prevent>
    <h4>Creating of post</h4>
    <my-input v-model="post.body" type="text" placeholder="name"/>
    <my-input v-model="post.title" type="text" placeholder="description"/>
    <my-button @click="createPost">Create</my-button>
  </form>
</template>

<style scoped>
form {
  display: flex;
  flex-direction: column;
}

</style>