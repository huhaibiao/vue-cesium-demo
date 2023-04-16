<!--
 * @Author: error: git config user.name && git config user.email & please set dead value or install git
 * @Date: 2022-10-14 14:47:50
 * @LastEditors: error: git config user.name && git config user.email & please set dead value or install git
 * @LastEditTime: 2022-10-14 15:17:09
 * @FilePath: /vue-cesium-demo/src/components/TodoList.vue
 * @Description: 
 * 
 * Copyright (c) 2022 by error: git config user.name && git config user.email & please set dead value or install git, All Rights Reserved. 
-->
<script setup lang="ts">
import { reactive, ref } from 'vue'
import ListItem from './ListItem.vue'
// import { todoList } from './state.js'
let value = ref('')

let enter = () => {
  console.log(value.value)
}
const todoList = reactive<string[]>([])
const add = () => {
  todoList.push(value.value)
  value.value = ''
}
const count = ref(0)
</script>

<template>
  <div>
    <input placeholder="请输入任务名称" v-model="value" @keydown.enter="enter" />
    <button @click="add">新建</button>
  </div>

  <div class="todo-list">
    <ListItem v-for="(item, index) of todoList" :index="index" :todoList="todoList" />
  </div>
</template>

<style scoped>
.todo-list {
  border: 2px solid;
}
</style>
