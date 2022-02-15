<template>
  <div class="row todo-content">
    <div class="col-md-6">
      <div class="todo-list not-done">
        <h1>TODOS</h1>
        <div class="input-group mb-3">
          <input type="text" class="form-control" placeholder="Enter content" v-model="textContent">
          <div class="input-group-append">
            <button class="btn btn-outline-secondary" type="button" id="button-addon2" @click="addTask()">Add</button>
          </div>
        </div>
        <hr>
        <ul class="list-unstyled" v-for="(todo) in todos" :key="todo.id">
          <li class="ui-state-default li-items mt-1">
            <div class="input-group">
              <div class="input-group-prepend">
                <div class="input-group-text">
                  <input type="checkbox" aria-label="Radio button for following text input" :checked="todo.checked" v-model="todo.checked">
                </div>
              </div>
              <input type="text" class="form-control" v-bind:class="{ 'done-task': todo.completed }" v-model="todo.content">
              <div class="input-group-append remove-icon">
                <span class="input-group-text" @click="delTask(todo.id)">&#10060;</span>
              </div>
            </div>
          </li>
        </ul>
        <hr>
        <div class="todo-footer row">
          <div class="col-md-6">
            <div class="form-check form-check-inline" @click="checkAll(true)">
              &#9989;
              <label class="form-check-label" for="inlineRadio1">Check all</label>
            </div>
            <div class="form-check form-check-inline" @click="checkAll(false)">
              &#10062;
              <label class="form-check-label" for="inlineRadio2">UnCheck all</label>
            </div>
          </div>
          <div class="col-md-6 save-all">
            <div class="form-check form-check-inline save-all">
              <button type="button" class="btn btn-success btn-sm" @click="doneAll()">DONE ALL &#10004;</button>
            </div>
            <div class="form-check form-check-inline save-all">
              <button type="button" class="btn btn-dark btn-sm" @click="delAll()">DEL ALL &#10006;</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Todos',
  data: function () {
    return {
      todos: [],
      textContent: '',
      code: 2
    }
  },
  created () {
    axios.get('http://localhost:8000/api/index/')
      .then(response => {
        this.todos = response.data.data
      })
  },
  methods: {
    addTask: function () {
      if (this.textContent.trim().length === 0) {
        return
      }
      let params = {
        'content': this.textContent,
        'checked': false,
        'completed': false
      }
      axios.post('http://localhost:8000/api/add/', params)
        .then(response => {
          this.todos = response.data.data
        })
      this.textContent = ''
    },
    delTask: function (id) {
      if (confirm('Chac chan REMOVE chu?')) {
        axios.post('http://localhost:8000/api/remove/', {id: id})
          .then(response => {
            this.todos = response.data.data
          })
      }
    },
    checkAll: function (flag) {
      this.todos.forEach(todo => {
        todo.checked = flag
      })
    },
    doneAll: function () {
      if (confirm('Chac chan DONE chu?')) {
        let params = this.todos
        axios.post('http://localhost:8000/api/doneAll/', { params })
          .then(response => {
            this.todos = response.data.data
            this.checkAll(false)
          })
      }
    },
    delAll: function () {
      if (confirm('Chac chan REMOVE het chu?')) {
        let params = this.todos
        axios.post('http://localhost:8000/api/removeAll/', { params })
          .then(response => {
            this.todos = response.data.data
          })
      }
    }
  }
}
</script>

<!-- Thêm "scoped" giới hạn CSS bên dưới chỉ áp dụng cho component này -->
<style scoped>
  .todo-content {
    display: flex;
    justify-content: center;
  }

  .todo-list h1 {
    margin-top: 20px;
    padding-bottom: 20px;
    text-align: center;
    font-weight: bold;
  }

  .todo-footer {
    background-color: #d2e8ca;
    padding: 10px 20px 15px;
  }

  #done-items li {
    padding: 10px 0;
    border-bottom: 1px solid #ddd;
    text-decoration: line-through;
  }

  .done-task {
    text-decoration: line-through;
    color: orange;
  }

  .form-check-inline {
    cursor: pointer;
  }

  .remove-icon span {
    cursor: pointer;
  }

  .save-all {
    float: right;
  }
</style>
