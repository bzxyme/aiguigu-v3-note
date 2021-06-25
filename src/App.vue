<template>
  <div class="todo-container">
    <div class="todo-wrap">
      <Header :addTodo="addTodo" />
      <list :todos="todos" :deleteTodo="deleteTodo" :updateTodo="updateTodo" />
      <Footer
        :todos="todos"
        :checkAll="checkAll"
        :clearAllCompletedTodo="clearAllCompletedTodo"
      />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, reactive, toRefs, watch } from "vue";

import Header from "@/components/Header.vue";
import List from "@/components/List.vue";
import Footer from "@/components/Footer.vue";

import { Todo } from "./types/todo";
import { saveTodos, readTodos } from "./utils/localStorageUtils";

export default defineComponent({
  name: "App",
  components: {
    Header,
    List,
    Footer,
  },
    // 界面加载完毕后过了一会再读取数据

  setup() {
    onMounted(() => {
      setTimeout(() => {
        state.todos = readTodos();
      }, 1000);
    });

    const state = reactive<{ todos: Todo[] }>({
      todos: [],
    });
    // 添加数据的方法

    const addTodo = (todo: Todo) => {
      state.todos.unshift(todo);
    };
    // 删除数据的方法

    const deleteTodo = (index: number) => {
      state.todos.splice(index, 1);
    };
    // 修改todo的isCompleted属性的状态

    const updateTodo = (todo: Todo, isCompleted: boolean) => {
      todo.isCompleted = isCompleted;
    };
    // 全选或者是全不选的方法

    const checkAll = (isCompleted: boolean) => {
      state.todos.forEach((element) => {
        element.isCompleted = isCompleted;
      });
    };

    watch(() => state.todos, saveTodos, { deep: true });
    // 清理所有选中的数据

    const clearAllCompletedTodo = () => {
      state.todos = state.todos.filter((todo) => !todo.isCompleted);
    };

    return {
      ...toRefs(state),
      addTodo,
      deleteTodo,
      updateTodo,
      checkAll,
      clearAllCompletedTodo,
    };
  },
});
</script>

<style>
/*app*/
.todo-container {
  width: 600px;
  margin: 0 auto;
}
.todo-container .todo-wrap {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}
</style>
