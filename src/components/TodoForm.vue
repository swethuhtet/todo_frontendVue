<script>
export default {
  data() {
    return {
      newTodo: ''
    }
  },
  emit: ['create'],
  methods: {
    onSubmit() {
      this.$emit('create', this.newTodo)
      this.newTodo = ''
    }
  }
}
</script>

<template>
  <div>
    <form @submit.prevent="onSubmit">
      <h2 class="label-wrapper">
        <label class="label__lg">What else to be done?</label>
      </h2>
      <input v-model="newTodo" required placeholder="Add new todo" class="input__lg" />
      <button type="submit" class="btn btn__primary btn__lg">Add</button>
    </form>
  </div>
</template>
