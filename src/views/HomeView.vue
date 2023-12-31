<script setup lang="ts">
import BannerSmall from '@/components/overwatch/BannerSmall.vue';
import TextBox from '@/components/ui/TextBox.vue';
import { searchPlayerByName } from '@/owProfile';
import { computed, ref, watchEffect } from 'vue';
import OnlinePlayerCard from '@/components/overwatch/OnlinePlayerCard.vue';
import useImperativeFetch from '@/composables/useImperativeFetch';
import CheckBox from '@/components/ui/CheckBox.vue';

const battleTagRegex = /^[^#]+(?:#\d+)?$/;

const searchName = ref('');
const includePrivate = ref(false);
const validationError = computed(() => {
  const tag = searchName.value;
  if (tag.length === 0 || battleTagRegex.test(tag)) {
    return null;
  } else {
    return 'Invalid name';
  }
});

const { fetching, result, fetch } = useImperativeFetch(searchPlayerByName, 500);

watchEffect(() => {
  if (!validationError.value && searchName.value !== '') {
    fetch(searchName.value.split('#')[0]);
  }
});

const filteredResult = computed(() => {
  if (!result.value || 'error' in result.value) {
    return result.value;
  } else {
    const resultCopy = { ...result.value };
    if (searchName.value.split('#').length === 2) {
      const tag = searchName.value.split('#')[1];
      resultCopy.results = resultCopy.results.filter(x => x.player_id.split('-')[1].includes(tag));
    }
    if (!includePrivate.value) {
      resultCopy.results = resultCopy.results.filter(x => x.privacy === 'public');
    }
    return resultCopy;
  }
});
</script>

<template>
  <div class="w-full h-full grid grid-rows-[min-content_1fr]">
    <header>
      <div class="flex flex-col w-full items-center p-4 box-border">
        <BannerSmall />
      </div>
    </header>
    <div class="flex flex-col items-center overflow-y-scroll">
      <TextBox
        v-model="searchName"
        class="w-2bw m-4 mb-1"
        :class="{ 'animate-pulse': fetching }"
        placeholder="Type in your Battle Tag"
        :is-valid="!validationError"
      />
      <span v-if="!!validationError" class="text-xs text-red-600">{{ validationError }}</span>
      <CheckBox class="m-2" v-model="includePrivate" label="Include private profiles" />
      <span v-if="!validationError && !!filteredResult && 'error' in filteredResult" class="mt-4 leading-10">{{
        filteredResult.error
      }}</span>
      <div
        v-else-if="!validationError && !!filteredResult && !('error' in filteredResult)"
        class="my-4 flex flex-1 flex-wrap items-center justify-center gap-4 w-full"
      >
        <OnlinePlayerCard
          v-for="item in filteredResult.results"
          :key="item.player_id"
          :player-id="item.player_id"
          :search-item="item"
        />
      </div>
    </div>
  </div>
</template>
