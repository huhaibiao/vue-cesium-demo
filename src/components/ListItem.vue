<!--
 * @Author: error: git config user.name && git config user.email & please set dead value or install git
 * @Date: 2022-10-14 14:47:50
 * @LastEditors: error: git config user.name && git config user.email & please set dead value or install git
 * @LastEditTime: 2022-10-14 15:31:11
 * @FilePath: /胡海标/src/components/ListItem.vue
 * @Description: 
 * 
 * Copyright (c) 2022 by error: git config user.name && git config user.email & please set dead value or install git, All Rights Reserved. 
-->
<script setup lang="ts">
import { reactive, ref } from 'vue'

import { defineProps } from 'vue'
const props = defineProps({
  index: {
    type: Number,
    default: () => {
      return 0
    }
  },
  todoList: {
    type: Array,
    default: () => {
      return []
    }
  }
})
const emit = defineEmits(['change', 'update'])
const delFn = () => {}
let itemShow = ref(true)
let rightBtnText = ref('修改')
const reWrite = () => {
  if (itemShow.value) {
    itemShow.value = false
    rightBtnText.value = '保存'
  } else {
    itemShow.value = true
    rightBtnText.value = '修改'
  }
}
</script>
<template>
  <div class="item" v-show="itemShow">
    {{ props.todoList[props.index] }}
  </div>
  <div v-show="!itemShow">
    <input v-model="props.todoList[props.index]" />
  </div>
  <div class="btns">
    <div class="del" @click="delFn">删除</div>
    <div class="put" @click="reWrite">{{ rightBtnText }}</div>
  </div>
</template>

<style scoped>
.btns {
  display: flex;
}
.btns div {
  margin-left: 20px;
  border: 1px solid;
}
</style>
