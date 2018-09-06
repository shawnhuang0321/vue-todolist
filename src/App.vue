<template>
  <div id="app" @keyup.esc="closeEdit">
    <!-- todo-nav -->
    <div class="bg-primary">
      <div class="container d-flex justify-content-between todo-nav">
        <a href="#" :class="{ 'active': currenStatus == 'all' }" @click="currenStatus = 'all'">My Tasks</a>
        <a href="#" :class="{ 'active': currenStatus == 'progress' }" @click="currenStatus = 'progress'">In Progress</a>
        <a href="#" :class="{ 'active': currenStatus == 'done' }" @click="currenStatus = 'done'">Completed</a>
      </div>
    </div>
    <div class="container my-4" v-if="!isNewTodo">
      <div class="position-relative">
        <i class="fas fa-plus fa-lg position-absolute text-primary" style="left: 1rem; top: 1.15rem"></i>
        <input type="text" class="form-control form-control-lg pl-5" id="" placeholder="Type something" @click="isNewTodo = true">
      </div>
    </div>
    <edit-todo-item 
      @closeEditTodo="closeEdit"
      @updateData="getData"
      v-if="isNewTodo">
    </edit-todo-item>

    <draggable @end="dragItem" v-model="todos" :options="{handle:'.handle'}">
      <div :todo="item" v-for="item in filterTodos" :key="item.id">
        <todo-item :todo="item"
          v-if="currentTodo.id !== item.id"
          @updateData="getData"
          @editTodoItem="editTodo"
          @deleteTodoItem="deleteTodo">
        </todo-item>
        <edit-todo-item :todo="currentTodo" 
          v-if="currentTodo.id === item.id"
          @closeEditTodo="closeEdit"
          @updateData="getData">
        </edit-todo-item>
      </div>
      
    </draggable>
    
    <Footer/>
  </div>
</template>

<script>
import TodoItem from './components/TodoItem'
import EditTodoItem from './components/EditTodoItem'
import Footer from './components/Footer'

import draggable from 'vuedraggable'

export default {
  name: 'App',
  components: {
    draggable,
    TodoItem,
    EditTodoItem,
    Footer
  },
  data () {
    return {
      todos: [],
      isNewTodo: false,
      currenStatus: 'all',
      currentTodo: {},
      sortData: []
    }
  },
  methods: {
    closeEdit () {
      this.isNewTodo = false;
      this.currentTodo = {};
    },
    getData () {
      const vm = this;
      const api = 'http://localhost:3000/todos';
      const sortapi = 'http://localhost:3000/sort';
      let todos = [];
      vm.todos = [];
      this.$http.get(api).then((response) => {
        // 將資料暫存至 todos
          todos = response.data;
          return vm.$http.get(sortapi)
          .then((response) => {
            const sortData = response.data.sort;
            // 整理排序 如有排序 push 至 vm.todos
            if (sortData) {
              sortData.forEach((sortID, index) => {
                const todo = todos.find((item) => {
                  return item.id === sortID;
                })
                vm.todos.push(todo);
              })
              // 若無排序 尋找出無排序的資料 push 至 vm.todos
              todos.forEach((todo) => {
                const hasSort = sortData.some((sortID) => {
                  return sortID === todo.id;
                })
                if (!hasSort) {
                  vm.todos.push(todo)
                }
              })
            } else {
              vm.todos = todos;
            }
          })
      })
    },
    editTodo (id) {
      const vm = this;
      vm.currentTodo = vm.todos.find((todo) => {
        return todo.id === id
      })
    },
    deleteTodo (id) {
      const vm = this;
      const idList = vm.todos.map((todo) => {
        return todo.id
      })
      vm.todos.splice(idList.indexOf(id), 1);
      vm.dragItem();
    },
    dragItem () {
      const vm = this;
      const sortapi = 'http://localhost:3000/sort';
      const sort = vm.todos.map((item) => {
        return item.id;
      })
      this.$http.post(sortapi, {sort: sort}).then((response) => {
        
      })
    }
  },
  computed: {
    filterTodos () {
      const vm = this;
      if (vm.currenStatus == 'progress') {
        return vm.todos.filter((todo) => {
          return todo.completed == false
        })
      } else if (vm.currenStatus == 'done') {
        return vm.todos.filter((todo) => {
          return todo.completed == true
        })
      } else {
        return vm.todos
      }
    }
  },
  created () {
    this.getData();
  }
}
</script>

<style lang="scss">

</style>
