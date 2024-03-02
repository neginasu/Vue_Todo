<script setup lang="ts">
import { ref } from 'vue';

type Todo = {
  id: number;
  task: string;
};

const todoTaskRef = ref('');
const todoListRef = ref<Todo[]>([]);
const currentEditIdRef = ref<number | null>(null);
const isEditRef = ref(false);
const ls = localStorage.todoList;
todoListRef.value = ls ? JSON.parse(ls): [];

const addTodo = () => {
  if (isEditRef.value) {
    const index = todoListRef.value.findIndex(todo => todo.id === currentEditIdRef.value);
    if (index !== -1) {
      todoListRef.value[index].task = todoTaskRef.value;
    }
    isEditRef.value = false;
    currentEditIdRef.value = null;
  } else {
    const id = new Date().getTime();
    todoListRef.value.push({ id: id, task: todoTaskRef.value });
  }
  localStorage.todoList = JSON.stringify(todoListRef.value);
  todoTaskRef.value = '';
};

const showTodo = (id: number) => {
  const todo = todoListRef.value.find((todo) => todo.id === id);
  todoTaskRef.value = todo?.task ?? '';
  currentEditIdRef.value = id;
  isEditRef.value = true;
};

const deleteTodo = (id: number) => {
  const idx = todoListRef.value.findIndex((todo) => todo.id === id);
  if (idx !== -1) {
    const todo = todoListRef.value[idx];
    const delMsg = `「${todo.task}」を削除しますか？`;
    if (!confirm(delMsg)) return;
    todoListRef.value.splice(idx, 1);
    localStorage.todoList = JSON.stringify(todoListRef.value);
  }
};

</script>

<template>
  <div class="box_input">
    <input
      v-model="todoTaskRef"
      type="text"
      class="todo_input"
      placeholder="TODOを入力"
    />
    <button class="btn" @click="addTodo">{{ isEditRef ? '変更' : '追加' }}</button>
  </div>
  <div class="box_list">
    <div v-for="todo in todoListRef" :key="todo.id" class="todo_list" >
      <div class="todo">
        <input type="checkbox" class="check" /><label>{{todo.task}}</label>
      </div>
      <div class="btns">
        <button class="btn green" @click="showTodo(todo.id)">編</button>
        <button class="btn pink" @click="deleteTodo(todo.id)">削</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.box_input {
  display: flex;
  margin-top: 20px;
}

.todo_input {
  width: 300px;
  margin-right: 8px;
  padding: 6px;
  font-size: 18px;
  border: 1px solid #aaa;
  border-radius: 6px;
}

.btn {
  padding: 8px;
  background-color: #03a9f4;
  border-radius: 6px;
  color: #fff;
  text-align: center;
  font-size: 14px;
  border: none;
}

.box_list {
  margin-top: 20px;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.todo_list {
  display: flex;
  align-items: center;
  gap: 8px;
}

.todo {
  border: 1px solid #ccc;
  border-radius: 6px;
  padding: 12px;
  width: 300px;
}

.check {
  border: 1px solid red;
  transform: scale(1.6);
  margin: 0 16px 2px 6px;
}

.btns {
  display: flex;
  gap: 4px;
}

.green {
  background-color: #00c853;
}

.pink {
  background-color: #ff4081;
}
</style>
