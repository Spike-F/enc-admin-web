
<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>

<script>
export default {
  name: 'Update',
  data() {
    return {
      count: 0
    }
  },

  // methods 是一些用来更改状态与触发更新的函数
  // 它们可以在模板中作为事件处理器绑定
  methods: {
    increment() {
      this.count++
    }
  }
}
</script>

<style scoped>

</style>
