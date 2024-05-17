<script>
export default{
    data(){
        return{
            todoList : ''
        }
    },
    emit:['createList'],
    methods:{
        onSubmit(){
            this.$emit('createList',this.todoList)
            this.todoList = ''
        }
    }
}
</script>

<template>
  <div>
    <form @submit.prevent="onSubmit">
      <h2 class="label-wrapper">
        <label class="label__lg"> Add new Todo List</label>
      </h2>
      <input v-model="todoList" required placeholder="Name a List" class="input__lg" />
      <button type="submit" class="btn btn__primary btn__lg">Add</button>
    </form>
  </div>
</template>
