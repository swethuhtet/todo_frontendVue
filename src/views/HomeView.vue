<script>
import TodoItem from '../components/TodoItem.vue'
import TodoForm from '../components/TodoForm.vue'
import axios from 'axios'

export default {
  components: {
    TodoItem,
    TodoForm
  },
  data() {
    return {
      todos: [],
      listid: '',
      listname: ''
    }
  },
  mounted() {
    const todolistID = this.$route.params.id
    this.fetchJson(todolistID)
    this.listname = this.$route.query.name
    this.listid = todolistID
  },
  methods: {
    fetchJson(todolistID) {
      axios
        .get(`http://localhost:3000/todolists/${todolistID}/todoitems`)
        .then((response) => {
          this.todos = response.data
        })
        .catch((error) => {
          console.error('Error fetching todos:', error)
        })
    },
    handleSubmit(msg) {
      const newTodo = { label: msg, done: false, todolist_id: this.listid }
      axios
        .post(`http://localhost:3000/todolists/${this.listid}/todoitems`, newTodo)
        .then((response) => {
          console.log('Todo added:', response.data.label)
          this.fetchJson(this.listid)
        })
        .catch((error) => {
          console.error('Error adding todo:', error)
        })
    },

    updateStatus(todoID) {
      const todoItem = this.todos.find((item) => item.id == todoID)
      const newTodo = {
        done: !todoItem.done
      }
      axios
        .patch(`http://localhost:3000/todolists/${this.listid}/todoitems/${todoID}`, newTodo)
        .then((response) => {
          console.log('Todo Status updated:', response.data.done)
          this.fetchJson(this.listid)
        })
        .catch((error) => {
          console.error('Error updating todo:', error)
        })
    },

    editTodo(todoId, newLabel) {
      const newTodo = {
        label: newLabel
      }
      axios
        .patch(`http://localhost:3000/todolists/${this.listid}/todoitems/${todoId}`, newTodo)
        .then((response) => {
          console.log('Todo updated:', response.data.label)
          this.fetchJson(this.listid)
        })
        .catch((error) => {
          console.error('Error updating todo:', error)
        })
    },
    deleteToDo(todoId) {
      axios
        .delete(`http://localhost:3000/todolists/${this.listid}/todoitems/${todoId}`)
        .then((response) => {
          console.log('Todo deleted:', response.data.label)
          this.fetchJson(this.listid)
        })
        .catch((error) => {
          console.error('Error deleting todo:', error)
        })
    }
  },
  computed: {
    listSummary() {
      const finishedtodos = this.todos.filter((item) => item.done).length
      return `${finishedtodos} out of ${this.todos.length} tasks are done.`
    },
    sortedTodoList() {
      return this.todos.slice().sort((a, b) => {
        if (a.done && !b.done) {
          return 1;
        } else if (!a.done && b.done) {
          return -1;
        } else {
          return 0;
        }
      });
    }
  }
}
</script>

<template>
  <main>
    <h1>
      {{ this.listname }}
    </h1>
    <TodoForm @create="(msg) => handleSubmit(msg)"></TodoForm>

    <ul aria-labelledby="list-summary" class="stack-large">
      <li v-for="item in sortedTodoList" :key="item.id">
        <TodoItem
          :label="item.label"
          :done="item.done"
          :id="item.id"
          :time = "item.created_at"
          @toggleDone="updateStatus(item.id)"
          @edited-item="(newLabel) => editTodo(item.id, newLabel)"
          @item-deleted="deleteToDo(item.id)"
        />
      </li>
    </ul>

    <h3>{{ listSummary }}</h3>
  </main>
</template>

<style>
/* Global styles */
.btn {
  padding: 0.8rem 1rem 0.7rem;
  border: 0.2rem solid #4d4d4d;
  cursor: pointer;
  text-transform: capitalize;
}
.btn__danger {
  color: #fff;
  background-color: #ca3c3c;
  border-color: #bd2130;
}
.btn__filter {
  border-color: lightgrey;
}
.btn__danger:focus {
  outline-color: #c82333;
}
.btn__primary {
  color: #fff;
  background-color: #000;
}
.btn__update {
  color: #fff;
  background-color: #0659f4;
}
.btn-group {
  display: flex;
  justify-content: space-between;
}
.btn-group > * {
  flex: 1 1 auto;
}
.btn-group > * + * {
  margin-left: 0.8rem;
}
.label-wrapper {
  margin: 0;
  flex: 0 0 100%;
  text-align: center;
}
[class*='__lg'] {
  display: inline-block;
  width: 100%;
  font-size: 1.9rem;
}
[class*='__lg']:not(:last-child) {
  margin-bottom: 1rem;
}
@media screen and (min-width: 620px) {
  [class*='__lg'] {
    font-size: 2.4rem;
  }
}
.visually-hidden {
  position: absolute;
  height: 1px;
  width: 1px;
  overflow: hidden;
  clip: rect(1px 1px 1px 1px);
  clip: rect(1px, 1px, 1px, 1px);
  clip-path: rect(1px, 1px, 1px, 1px);
  white-space: nowrap;
}
[class*='stack'] > * {
  margin-top: 0;
  margin-bottom: 0;
}
.stack-small > * + * {
  margin-top: 1.25rem;
}
.stack-large > * + * {
  margin-top: 2.5rem;
}
@media screen and (min-width: 550px) {
  .stack-small > * + * {
    margin-top: 1.4rem;
  }
  .stack-large > * + * {
    margin-top: 2.8rem;
  }
}
/* End global styles */
#app {
  background: #fff;
  margin: 2rem 0 4rem 0;
  padding: 1rem;
  padding-top: 0;
  position: relative;
  box-shadow:
    0 2px 4px 0 rgb(0 0 0 / 20%),
    0 2.5rem 5rem 0 rgb(0 0 0 / 10%);
}
@media screen and (min-width: 550px) {
  #app {
    padding: 4rem;
  }
}
#app > * {
  max-width: 50rem;
  margin-left: auto;
  margin-right: auto;
}
#app > form {
  max-width: 100%;
}
#app h1 {
  display: block;
  min-width: 100%;
  width: 100%;
  text-align: center;
  margin: 0;
  margin-bottom: 1rem;
}
</style>
