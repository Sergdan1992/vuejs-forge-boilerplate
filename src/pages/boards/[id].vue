<script setup lang="ts">
import { toRefs, ref } from "vue";
import type { Board, Task } from "@/types";

import { v4 as uuidv4 } from "uuid";
import { useAlerts } from "@/stores/Alerts";

const alerts = useAlerts();

const props = defineProps<{
  id: string;
}>();

const { id: boardId } = toRefs(props);

const board = ref<Partial<Board>>({
  id: boardId.value,
  title: "Hardcode title",
  order: JSON.stringify([{ id: "1", title: "backlog", taskIds: ["1", "2"] }]),
});

const tasks = ref<Partial<Task>[]>([
  { id: 1, title: "Make toast", dueAt: new Date("2022/8/17") },
  { id: 2, title: "Clear room" },
]);

function addTask(task: Task) {
  return new Promise((resolve, reject) => {
    const taskWithId = {
      ...task,
      id: uuidv4(),
    };
    task.value.push(taskWithId);
    resolve(taskWithId);
  });
}

function updateBoard(b: Board) {
  board.value = b.value;
  alerts.success("Board updated!");
}
function deleteBoardIfConfirmed() {
  const answer = confirm("Are you sure you want to delete this board?");
  if (answer) {
    alerts.success("Board deleted!");
  }
}
</script>

<template>
  <!-- <AppImageDropzone /> -->
  <div class="flex justify-between">
    <AppPageHeading>{{ board.title }}</AppPageHeading>
    <BoardMenu :board="board" @deleteBoard="deleteBoardIfConfirmed" />
  </div>
  <BoardDragAndDrop
    :board="board"
    :tasks="tasks"
    @update="updateBoard"
    :addTask="addTask"
  />
</template>
