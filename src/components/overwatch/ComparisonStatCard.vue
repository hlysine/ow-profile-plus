<script setup lang="ts">
import { computed } from 'vue';

const props = defineProps<{
  leftTitle: string;
  leftStat: string;
  rightTitle: string;
  rightStat: string;
  ratio: number;
}>();
const emit = defineEmits<{
  clickLeft: [payload: MouseEvent];
  clickRight: [payload: MouseEvent];
}>();

const growRatio = computed(() => props.ratio / (1 - props.ratio));
</script>

<template>
  <div class="relative w-2bw h-1bh rounded-sm bg-slate-800 bg-opacity-50 backdrop-blur overflow-hidden shadow">
    <div class="h-full w-full flex items-stretch">
      <div
        class="opacity-50 bg-gradient-to-t from-blue-700 via-transparent via-75% to-transparent basis-0 transition-all"
        :style="{ 'flex-grow': isFinite(growRatio) ? growRatio : 1 }"
      />
      <div
        class="opacity-50 bg-gradient-to-t from-red-700 via-transparent via-75% to-transparent basis-0 transition-all"
        :style="{ 'flex-grow': isFinite(growRatio) ? 1 : isFinite(ratio) ? 0 : 1 }"
      />
    </div>
    <div class="absolute inset-0 flex flex-col p-4">
      <div class="flex items-center">
        <span class="flex-1 uppercase text-slate-400">{{ leftTitle }}</span>
        <slot name="icon" />
        <span class="flex-1 uppercase text-slate-400 text-right">{{ rightTitle }}</span>
      </div>
      <div class="flex flex-1 items-center">
        <span class="flex-1 text-blue-400 text-5xl truncate">{{ leftStat }}</span>
        <span class="flex-1 text-red-400 text-5xl truncate text-right">{{ rightStat }}</span>
      </div>
    </div>
    <div class="absolute inset-0 flex items-stretch justify-stretch">
      <button
        class="flex-1 rounded-none bg-gradient-to-r from-slate-300 to-transparent opacity-0 hover:opacity-5 active:opacity-10 transition-opacity"
        @click="e => emit('clickLeft', e)"
      />
      <button
        class="flex-1 rounded-none bg-gradient-to-l from-slate-300 to-transparent opacity-0 hover:opacity-5 active:opacity-10 transition-opacity"
        @click="e => emit('clickRight', e)"
      />
    </div>
  </div>
</template>
