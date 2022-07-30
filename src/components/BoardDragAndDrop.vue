<script setup lang="ts">
import clone from "just-clone";
import { reactive, ref, watch, toRaw } from "vue";
import type { Board, Task, Columns } from "@/types";
import { v4 as uuidv4 } from "uuid";
import draggable from "vuedraggable";
import TaskCard from "./TaskCard.vue";
import { useAlerts } from "@/stores/Alerts";

const alerts = useAlerts();

// props
const props = defineProps<{
  board: Board;
  tasks: Task[];
  addTask(task: Partial<Task>): Promise<Task>;
}>();

// events
const emit = defineEmits<{
  (e: "update", payload: Partial<Board>): void;
}>();

// data
const tasks = ref(clone(props.tasks));
const board = ref(clone(props.board));
const columns = reactive<Columns[]>(JSON.stringify(board.value.order as string));

// methods
function addColumn() {
  columns.push({
    id: uuidv4(),
    title: 'New title',
    taskIds: [],
  })
}

// watch
watch(columns, () => {
  emit(
    'update',
    clone({ ...board, order: JSON.stringify(toRaw(columns))})
  );
});
</script>
<template>
  <div class="flex py-12 items-start">
    <draggable
      :list="columns"
      group="colums"
      item-key="id"
      class="flex overflow-x-scroll flex-grow-0 flex-shring-0"
    >
      <template #item="{ element: column }">
        <div class="column bg-gray-100 flex flex-col justify-between rounded-lg rounded-lg px-3 py-3 mr-4 w-[300px]">
          <div>
            <h3>{{ column.title }}</h3>
            <draggable
              :list="column.taskIds"
              group="task"
              item-key="uid"
              :animation="200"
              ghost-class="ghost-card"
              class="min-h-[400px]"
            >
              <template #item="{ element: taskId }">
                <TaskCard
                  v-if="tasks.find((t: Task) => t.id === taskId)"
                  :task="(tasks.find((t: Task) => t.id === taskId) as Task)"

                />
              </template>
            </draggable>
          </div>
          <TaskCreator @create="addTask({ column, title: $event })" />
        </div>
      </template>

    </draggable>
  </div>
</template>
