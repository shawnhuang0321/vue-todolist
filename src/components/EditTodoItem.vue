<template>
    <div>
        <!-- Edit-todo-item -->
        <div class="container mt-4 todo-header edit-todo-item">
        <!-- todo-header -->
        <div class="text-center">
            <input type="text" class="form-control mx-3" placeholder="Type something" autofocus v-model="cacheTodo.message">
            <h4 class="text-left text-danger" style="margin: 10px 0 0 16px; padding: 5px 5px;" v-if="alertText">請輸入訊息</h4>
        </div>
        </div>
        <div class="container edit-todo-item mt-1">
        <!-- todo-content -->
        <div class="todo-row px-4 py-2">
            <!-- deadline -->
            <div class="mx-12px fz-24px">
            <i class="far fa-calendar-alt"></i>
            </div>
            <div>
            <label for="" class="fz-24px">Deadline</label>
            <div class="form-inline">
                From <input type="date" class="form-control" v-model="cacheTodo.startDate">
                To <input type="date" class="form-control" v-model="cacheTodo.endDate">
            </div>
            </div>
        </div>
        <!-- file -->
        <div class="todo-row px-4 py-2">
            <div class="mx-12px fz-24px">
            <i class="fas fa-file"></i>
            </div>
            <div>
            <label for="" class="fz-24px">File</label>
            <div class="form-inline">
                <a href="#" class="fz-24px"><i class="fas fa-plus-square" style="color: #C8C8C8;"></i></a>
            </div>
            </div>
        </div>
        <!-- comment -->
        <div class="todo-row px-4 py-2">
            <div class="mx-12px fz-24px">
            <i class="far fa-comment-dots"></i>
            </div>
            <div class="w-100">
            <label for="" class="fz-24px">Comment</label>
            <div class="form-inline">
                <textarea class="form-control w-100 border-0" rows="5" id="" v-model="cacheTodo.comments"></textarea>
            </div>
            </div>
        </div>
        <!-- todo-footer -->
        <div class="d-flex pt-2">
            <button class="btn text-danger w-50 btn-lg p-3" @click="closeTodo">
            <i class="fas fa-times"></i> Cancel
            </button>
            <button class="btn btn-primary w-50 btn-lg p-3" @click="updateTodo">
            <i class="fas fa-plus"></i> Update Task
            </button>
        </div>
        </div>
    </div>
</template>

<script>
export default {
    props: ['todo'],
    name: "EditTodoItem",
    data () {
        return {
            cacheTodo: {},
            alertText: false,
            isNew: false
        };
    },
    methods: {
        updateTodo () {
            let timestamp = Math.floor(Date.now())
            const vm = this;
            const todo = {
                ...vm.cacheTodo,
            };
            // 若沒輸入 message
            if (!todo.message) {
                vm.alertText = true;
                return;
            }
            vm.alertText = false;
            // 若為新的 todo
            if (this.isNew) {
                const api = 'http://localhost:3000/todos';
                todo.stared = false;
                todo.completed = false;
                todo.comments = todo.comments || '';
                todo.timestamp = timestamp;
                this.$http.post(api, todo).then((response) => {
                    vm.$emit('closeEditTodo');
                    vm.$emit('updateData');
                })
            } else {
                //若為舊的 todo
                const api = `http://localhost:3000/todos/${vm.todo.id}`;
                this.$http.put(api, todo).then((response) => {
                    vm.$emit('closeEditTodo');
                    vm.$emit('updateData');
                })
            }
        },
        closeTodo () {
            this.$emit('closeEditTodo')
        }
    },
    watch: {
        todo () {
            this.cacheTodo = { ...this.todo }
        }
    },
    created () {
        if (!this.todo) {
            this.isNew = true
        } else {
            this.cacheTodo = { ...this.todo }
        }
    }
};
</script>
