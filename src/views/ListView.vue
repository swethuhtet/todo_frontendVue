<script>
import { RouterLink } from 'vue-router'
import ListForm from '../components/ListForm.vue'
import axios from 'axios'
export default {
  components: {
    ListForm
  },
  data() {
    return {
      todolists: []
    }
  },
  methods: {
    fetchLists() {
      axios
        .get('http://localhost:3000/todolists')
        .then((response) => {
          this.todolists = response.data
        })
        .catch((error) => {
          console.error('Error fetching todos:', error)
        })
    },
    handleList(listname) {
      const newListname = { name: listname, user_id: 1 }
      axios
        .post('http://localhost:3000/todolists', newListname)
        .then((response) => {
          console.log('List added:', response.data.name)
          this.fetchLists()
        })
        .catch((error) => {
          console.error('Error adding todo:', error)
        })
    }
  },
  mounted() {
    this.fetchLists()
  }
}
</script>
<template>
  <div>
    <ListForm @createList="(msg) => handleList(msg)" />
    <div class="list-container">
      <ul>
        <li v-for="i in todolists" :key="i.id">
          <div
            class="box-link"
            @click="$router.push({ name: 'todoitems', params: { id: i.id }, query: { name: i.name }})"
          >
            {{ i.name }}
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>
<style>
.box-link {
  border: 1px solid black;
  margin: 20px;
  width: 200px;
  height: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}
.list-container{
  display: flex;
  justify-content: space-around;
}
</style>
