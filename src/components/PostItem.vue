<script>
import MyButton from "@/components/UI/MyButton.vue";

export default {
  components: {MyButton},
  props: {
    post: {
      type: Object,
      required: true
    }
  }
}
</script>

<template>
  <div class="post">
    <div>
      <div>{{post.id}}</div>
      <div><strong>name: </strong>{{ post.title }}</div>
      <div><strong>description: </strong>{{ post.body }}</div>
      <div class="post__btns">
        <my-button @click="$emit('remove', post)">delete</my-button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.post{
  padding: 15px;
  border: 2px solid teal;
  margin-top: 15px;
  align-items: center;
  display: flex;
  justify-content: space-between;
}
</style>