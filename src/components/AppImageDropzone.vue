<script setup lang="ts">
import { ref } from "vue";
import { useDropZone, useBase64 } from "@vueuse/core";

const dropZoneRef = ref<HTMLElement | null>(null);
const { isOverDropZone } = useDropZone(dropZoneRef, onDrop);

const file = ref<File | undefined>();
const { base64: url } = useBase64(file);

function onDrop(files: File[] | null) {
  file.value = files[0];
}
function onFileChange(event: InputEvent) {
  file.value = event.target.files[0];
}
function resetFile() {
  file.value = null;
}
</script>
<template>
  <div ref="dropZoneRef" class="w-[400px] h-[400px] flex bg-slate-300 relative">
    <div class="m-auto opacity-50" :class="{ 'z-4': isOverDropZone }">
      Drop a image over or click to select
    </div>
    <div v-if="isOverDropZone" class="m-auto opacity-50">Drop here</div>
    <img
      v-if="url"
      class="absolute left-0 top-0 w-full h-full object-cover"
      :src="url"
    />
    <input
      class="absolute left-0 top-0 bottom-0 right-0 opacity-0"
      type="file"
      accept="image/*"
      @input="onFileChange"
    />
  </div>
  <AppButton @click="resetFile" icon="close">REset</AppButton>
</template>
