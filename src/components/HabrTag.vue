<template>
  <div class="habrtag">@{{ tag.alias }}
    <span class="habrtag__del" @click="deleteItem(tag.alias)" tabindex="0" aria-label="Удалить элемент">
      <CloseIcon />
    </span>
  </div>
</template>


<script lang="ts" setup>
import CloseIcon from './icons/CloseIcon.vue';
import { defineProps, type PropType } from "vue"

interface Suggest {
  type: string,
  alias: string,
  name: string,
  avatar: string,
}

defineProps({
  tag: {
    type: Object as PropType<Suggest>,
    required: true,
  }
})

const emits = defineEmits(["deleteItem"])

function deleteItem(alias: string) {
  emits("deleteItem", alias);
}
</script>


<style scoped>
.habrtag {
  padding: 6px;
  margin: 4px 0 5px 4px;
  font-size: 12px;
  line-height: 17px;
  font-weight: 700;
  color: var(--romance, #fff);
  background-color: var(--horizon, #8ba9bc);
  border-radius: 2px;
  /* cursor: pointer; */
}

.habrtag__del {
  margin-left: 4px;
  cursor: pointer;
}
</style>
