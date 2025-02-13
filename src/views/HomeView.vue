<template>
  <main>
    <div class="container">
      <SuggestItem :limit="1" api_url="https://habr.com/kek/v2/publication/suggest-mention" param_name="q"
        input_title="Пользователь или компания" input_placeholder="Введите логин" :required="true"
        @change-status="statusChanged" />
      <!-- <p class="base-chips__description tm-chips__description base-chips__description_hubs">Выберите от 1 до 5 хабов
          по теме публикации</p> -->
    </div>
  </main>
</template>

<script lang="ts" setup>
import SuggestItem from '../components/SuggestItem.vue';

interface Suggest {
  type: string,
  alias: string,
  name: string,
  avatar: string,
}

interface StatusInfo {
  status: string,
  value: Suggest[]
}

function statusChanged(status_info: StatusInfo) {
  console.log(status_info);
}
</script>


<style scoped>
.suggest {
  position: relative;
}

.suggest__main {
  border: 1px solid var(--loblolly, #d5dddf);
  width: 100%;
}

.suggest__field {
  border-radius: 3px;
  margin: 0;
  width: 100%;
  border: 1px solid var(--loblolly, #d5dddf);
  line-height: 1.5;
  color: var(--mine-shaft, #333);
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
  color: var(--fuzzy-wuzzy-brown, #ff8d85);
}

.suggest__item {
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

.suggest__item__del {
  margin-left: 4px;
  cursor: pointer;
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
}

.suggest__input::placeholder {
  color: #c0c0c0;
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
}

.suggest__list__item {
  padding: 10px;
  display: flex;
  align-items: center;
  gap: 10px;
  background: none;
  border: none;
  transition: background 0.3s;
  cursor: pointer;
}

.suggest__list__item:hover,
.suggest__list__item:focus {
  background-color: #E2E2E2;
  outline: none;
}

.suggest__list__img {
  width: 40px;
  height: 40px;
  object-fit: cover;
  border-radius: 5px;
}

.suggest__list__text {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 1px;
}
</style>
