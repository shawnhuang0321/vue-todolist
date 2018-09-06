<template>
  <div>
    <!-- todo-item -->
    <div class="container todo-header edit-todo-item mt-4" :class="{'item-stared': todo.stared}">
      <!-- todo-header -->
      <div class="d-flex align-items-center position-relative">
        <div class="handle"><i class="fas fa-ellipsis-v"></i></div>
        <input type="checkbox" v-model="todo.completed" @change="changeStatus('completed', todo.completed)" id="toto-check" class="todo-check">
        <label class="todo-title ml-3" :class="{ 'completed': todo.completed }">{{ todo.message }}</label>
        <!-- <input type="text" class="form-control mx-3" placeholder="Type something"> -->
        <div class="d-flex todo-header-icon">
          <a href="#" @click.prevent="changeStatus('stared', !todo.stared)">
            <i class="far fa-star" v-if="!todo.stared"></i>
            <i class="fas fa-star stared" v-else></i>
          </a>
          <a href="#" @click.prevent="editTodo"><i class="fas fa-pencil-alt ml-3"></i></a>
          <a href="#" @click.prevent="deleteTodo"><i class="fas fa-times ml-3"></i></a>
        </div>
      </div>
      <div class="todo-footer">
        <i class="far fa-calendar-alt fz-24px"></i> <span>{{todo.startDate}} - {{todo.endDate}}</span>
        <i class="fas fa-comment-dots fz-24px ml-3" v-if="todo.comments !== ''"></i>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TodoItem',
  props: ['todo'],
  data () {
    return {}
  },
  methods: {
    changeStatus (field, state) {
      const vm = this;
      const api = `http://localhost:3000/todos/${vm.todo.id}`;
      const todo = {
        ...vm.todo,
      }
      // 更新 name: value
      todo[field] = state;
      this.$http.put(api, todo).then((response) => {
        vm.$emit('updateData');
      })
    },
    editTodo () {
      this.$emit('editTodoItem', this.todo.id)
    },
    deleteTodo () {
      const vm = this;
      const api = `http://localhost:3000/todos/${vm.todo.id}`;
      this.$http.delete(api).then((response) => {
        vm.$emit('deleteTodoItem', this.todo.id)
        vm.$emit('updateData');
      })
    },
    changeCompleted () {
      this.$emit('updateData');
    }
  }
}
</script>
