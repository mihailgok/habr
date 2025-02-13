<template>
  <div class="suggest">
    <label class="suggest__label" :ariaLabel="input_title">
      <span>
        <span class="suggest__label__star" v-if="required"> * </span>
        {{ input_title }}
      </span>
    </label>
    <div class="suggest__main">
      <input type="hidden" form="" name="suggests[]" :value="item.alias" v-for="item of selectedItems"
        :key="item.alias">
      <div class="suggest__field">
        <HabrTag v-for="item of selectedItems" :key="item.alias" @deleteItem="deleteItem" :tag="item" />
        <div class="suggest__search">

          <input :placeholder="input_placeholder" class="suggest__input" type="text" v-model="inputValue"
            :disabled="selectedItems.length > limit - 1" @keydown="handleKeyDown($event, -1)" role="search">
          <div class="suggest__list">
            <div class="suggest__list__error" v-if="requestError">Ошибка</div>
            <!-- <div class="suggest__list__loader">Ошибка</div> -->
            <SuggestListItem :item="item" v-for="(item, index) of searchList" :key="item.alias"
              @keydown="handleKeyDown($event, index)" role="button" @addSuggest="addItem"
              :ariaLabel="item.name ? item.name : item.alias" />
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script lang="ts" setup>
import { ref, watch, defineProps } from "vue";

import SuggestListItem from "./SearchListItem.vue";
import HabrTag from "./HabrTag.vue";

interface Suggest {
  type: string,
  alias: string,
  name: string,
  avatar: string,
}

const props = defineProps({
  limit: {
    type: Number,
    required: false,
    default: 1,
  },
  api_url: {
    type: String,
    required: true,
  },
  param_name: {
    type: String,
    required: true,
  },
  input_title: {
    type: String,
    required: false,
    default: "Поле ввода"
  },
  input_placeholder: {
    type: String,
    required: false,
    default: "Введите запрос"
  },
  required: {
    type: Boolean,
    required: false,
    default: false
  }
})

const headers = {
  'Accept': 'application/json',
  'Content-Type': 'application/json'
};

const selectedItems = ref<Suggest[]>([]);
const inputValue = ref<string>("");
const searchList = ref<Suggest[]>([]);
const focusedIndex = ref(0);
const requestError = ref(false);

type ResponseData = {
  data: Suggest[]
}

const emits = defineEmits(["changeStatus"]);

async function searchCompany(query: string) {
  try {
    requestError.value = false;
    const searchParams = new URLSearchParams({ [props.param_name]: query });
    const url = `${props.api_url}?${searchParams.toString()}`;
    const response = await fetch(url, { headers });

    if (!response.ok) {
      throw new Error(`Сервер вернул ошибку: ${response.status}`);
    }

    const data: ResponseData = await response.json();

    if (!data['data'] || !Array.isArray(data['data']) || data['data'].length === 0) {
      console.log('Нет данных');
      searchList.value = [];
      return [];
    }

    searchList.value = data['data'];
    return data;
  } catch (error) {
    requestError.value = true;
    if (error instanceof Error && error.message.includes('400')) {
      console.error('Ошибка 400:', error.message);
    } else if (error instanceof Error && error.message.includes('500')) {
      console.error('Ошибка 500:', error.message);
    } else {
      console.error('Неизвестная ошибка');
    }

    searchList.value = [];
    return [];
  }
}

function deleteItem(alias: string) {
  selectedItems.value = selectedItems.value.filter(el => el.alias !== alias);
  emits("changeStatus", {
    status: "deleted",
    value: selectedItems.value
  })
}

function addItem(item: Suggest) {
  if (selectedItems.value.findIndex(el => el.alias === item.alias) === -1) {
    selectedItems.value.push(item);
    emits("changeStatus", {
      status: "added",
      value: selectedItems.value
    })
    inputValue.value = "";
    searchList.value = [];
  }
}

function debounce(func: (...args: any[]) => void, delay: number) {
  let timeout: ReturnType<typeof setTimeout> | null = null;

  const debouncedFunction = (...args: any[]) => {
    if (timeout) clearTimeout(timeout);
    timeout = setTimeout(() => {
      func(...args);
    }, delay);
  };

  debouncedFunction.cancel = () => {
    if (timeout) {
      clearTimeout(timeout);
      timeout = null;
    }
  };

  return debouncedFunction;
}

const debouncedSearchCompany = debounce(searchCompany, 300);
watch(inputValue, (newValue) => {
  requestError.value = false;
  if (newValue.length < 3) {
    searchList.value = [];
    debouncedSearchCompany.cancel();
    return;
  }

  debouncedSearchCompany(newValue);
});


function handleKeyDown(event: KeyboardEvent, index: number) {
  switch (event.key) {
    case 'ArrowUp':
      event.preventDefault();
      moveFocus(index - 1);
      break;
    case 'ArrowDown':
      event.preventDefault();
      moveFocus(index + 1);
      break;
    case 'Enter':
      event.preventDefault();
      if (index !== -1)
        addItem(searchList.value[index])
    default:
      break;
  }
}

function moveFocus(newIndex: number) {
  const listLength = searchList.value.length;
  if (newIndex >= 0 && newIndex < listLength) {
    focusedIndex.value = newIndex;

    if (searchList.value.length > 0) {
      const el = document.querySelectorAll('.search_item')[focusedIndex.value] as HTMLButtonElement;
      el.focus();
    }
  }
}

// searchCompany("МАИ");
</script>

<style scoped>
.suggest {
  position: relative;
}

.suggest__main {
  border: 1px solid #d5dddf;
  width: 100%;
}

.suggest__field {
  border-radius: 3px;
  margin: 0;
  width: 100%;
  border: 1px solid #d5dddf;
  line-height: 1.5;
  color: #333;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  outline: none;
  font-size: 14px;
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -ms-flex-wrap: wrap;
  flex-wrap: wrap;
}

.suggest__label {
  margin-bottom: 12px;
  display: inline-block;
}

.suggest__label__star {
  color: #ff8d85;
}

.suggest__input {
  color: #444;
  border-radius: 3px;
  display: block;
  margin: 0;
  padding: 0 14px;
  height: 38px;
  border: 0;
  outline: none;
  font-size: 14px;
  -webkit-box-flex: 1;
  -ms-flex: 1;
  flex: 1;
  width: 100%;
}

.suggest__input::placeholder {
  color: #c0c0c0;
}

.suggest__search {
  -webkit-box-flex: 1;
  -ms-flex: 1;
  flex: 1;
  position: relative;
  min-width: 200px;
}

.suggest__list {
  display: flex;
  flex-direction: column;
  box-shadow: -1px 8px 26px -14px rgba(66, 68, 90, 1);
  max-height: 240px;
  overflow-y: auto;
  position: absolute;
  top: 100%;
  left: 0;
  min-width: 200px;
  max-width: 100%;
}

.suggest__list__error {
  text-align: center;
  padding: 25px 20px 20px;
  min-width: 200px;
  color: #ff8d85;
}
</style>
