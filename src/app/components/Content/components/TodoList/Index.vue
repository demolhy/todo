<template>
  <div class="todo-list" @dblclick="addClick">
    <n-scrollbar class="item-list">
      <div style="padding-bottom: 20px">
        <TodoItem v-for="(item, index) in list" :todo="item" @refresh="refresh" :key="index" />
        <AddTodoInfo v-if="isEdit"  @close="closeClick" @ok="okClick" />
      </div>
    </n-scrollbar>
    
    <!-- <AddTodo class="is-add-todo" @refresh="refresh" /> -->
  </div>
</template>

<script setup lang="ts">
import { NScrollbar } from "naive-ui";
import TodoItem from "../TodoItem";
import AddTodo from "../AddTodo";
import { provide, ref, toRaw } from "vue";
import { TodoModel } from "@/common/interface";
import { ipcRenderer } from "@/app/utils/render";
import { refresh as refreshApi } from '@app/utils/event'
import { getTodo } from "@/app/utils/send";
import moment from "moment";
import AddTodoInfo from "../AddTodoInfo";
import { addTodo } from "@/app/utils/send";
import { playAddAudio } from "@/app/utils/audio";

refreshApi(() => {
  refresh();
})

const list = ref<Array<TodoModel>>([]);
// 刷新数据
const refresh = () => {
  list.value = getTodo().msg;
  console.log(list.value)
};
refresh();

const isEdit = ref(false);
// 关闭
const closeClick = () => {
  isEdit.value = false;
};
// 确认新增按钮
const okClick = (todo: TodoModel) => {
  const data = addTodo(todo);
  // if (data.code == 1) emits("refresh");
  closeClick();
  playAddAudio()
};
// 新增按钮
const addClick = () => {
  isEdit.value = true;
};
</script>

<style lang="less" scoped>
  .is-add-todo{
    // display: none;
    opacity: 0;
    transition: all 1s;
  }
.todo-list {
  margin: 8px 0px;
  height: calc(100% - 80px - 26px);
  // overflow: hidden;
  &:hover{
    .is-add-todo{
      display: block;
      // transition: all .5s;
      opacity: 1;
    }
  }

  .item-list {
    height: calc(100% - 120px);
    padding-bottom: 140px;
    // margin-top: 15px;
    overflow-y: auto;

    &::-webkit-scrollbar {
      display: none;
    }

    &:hover {
      &::-webkit-scrollbar {
        display: block;
        background: transparent;
        width: 5px;
      }

      &::-webkit-scrollbar-thumb {
        background: rgba(0, 0, 0, 0.4);
        border-radius: 10px;
      }
    }
  }
}
</style>
