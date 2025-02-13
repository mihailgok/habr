<template>
  <button class="search_item" @click="addItem(item)">
    <img :src="item.avatar" alt="" class="search_item__img" v-if="item.avatar">
    <ImageHolder class="search_item__img" v-else />
    <!-- <img src="../assets/placeholder.webp" alt="" class="search_item__img" v-else> -->

    <div class="search_item__text">
      <div class="search_item__name">{{ item.name ? item.name : item.alias }}</div>
      <div class="search_item__alias" v-if="item.type === 'company'">Компания</div>
      <div class="search_item__alias" v-else>@{{ item.alias }}</div>
    </div>
  </button>
</template>

<script lang="ts" setup>
import { type PropType, defineProps, defineEmits } from "vue";
import ImageHolder from "./icons/ImageHolder.vue";

interface Suggest {
  type: string,
  alias: string,
  name: string,
  avatar: string,
}

defineProps({
  item: {
    type: Object as PropType<Suggest>,
    required: true
  },
});

const emits = defineEmits(['addSuggest'])

function addItem(item: Suggest) {
  emits('addSuggest', item);
}
</script>


<style scoped>
.search_item {
  padding: 10px;
  display: flex;
  align-items: center;
  gap: 10px;
  background: none;
  border: none;
  transition: background 0.3s;
  cursor: pointer;
}

.search_item:hover,
.search_item:focus {
  background-color: #E2E2E2;
  outline: none;
}

.search_item__img {
  width: 40px;
  height: 40px;
  object-fit: cover;
  border-radius: 5px;
  flex-shrink: 0;
}

.search_item__text {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 1px;
  text-align: left;
}

.search_item__name {
  font-weight: 500;
  color: #000;
}

.search_item__alias {
  color: #8f8f8f;
}
</style>
