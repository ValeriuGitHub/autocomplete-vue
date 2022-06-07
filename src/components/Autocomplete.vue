<script setup>
  import { ref, watch } from "vue";

  const state = ref("");

  const modal = ref(false);

  const titles = ref([]);

  const filteredTitles = ref([]);

  watch(state, () => {
    let timeout = null;

    const debounce = () => {
      clearTimeout(timeout);

      timeout = setTimeout(() => {
        filterTitles();
      }, 300);
    };

    debounce();
  });

  const getTitles = async () => {
    if (!titles.value.length) {
      const todos = await fetch(
        "https://jsonplaceholder.typicode.com/todos"
      ).then((response) => response.json());

      titles.value = todos.map(({ title }) => title);

      filterTitles();
    }

    modal.value = true;
  };

  const filterTitles = async () => {
    if (!state.value) {
      filteredTitles.value = titles.value;
    }

    filteredTitles.value = titles.value.filter((title) => {
      return title.toLowerCase().startsWith(state.value.toLowerCase());
    });
  };

  const setState = (title) => {
    state.value = title;

    modal.value = false;
  };
</script>

<template>
  <div class="autocomplete">
    <div class="autocomplete__title">Autocomplete</div>
    <input
      type="text"
      v-model="state"
      @input="filterTitles"
      @focus="getTitles()"
      autocomplete="off"
      class="autocomplete__input"
    />
    <div class="autocomplete__outside" @click="modal = false"></div>

    <transition name="fade">
      <div v-if="filteredTitles.length && modal" class="autocomplete__list">
        <div
          v-for="(title, index) in filteredTitles"
          class="autocomplete__item"
          :key="index"
          @click="setState(title)"
        >
          {{ title }}
        </div>
      </div>
    </transition>
  </div>
</template>

<style lang="scss" scoped>
  .autocomplete {
    padding: 20px 0px;
    display: flex;
    flex-direction: column;
    align-items: center;
    font-family: "Arial";
    margin: auto;
    width: 300px;

    &__title {
      font-size: 24px;
      font-weight: bold;
      margin-bottom: 20px;
    }

    &__input {
      width: 100%;
      box-sizing: border-box;
      padding: 7.5px;
      z-index: 1;
      position: relative;
      font-size: 16px;
      border-radius: 5px;
      border: 1px solid gray;
      margin-bottom: 10px;
    }

    &__outside {
      position: absolute;
      width: 100%;
      z-index: 0;
      height: 100%;
      top: 0px;
    }

    &__list {
      background: ghostwhite;
      border-radius: 5px;
      width: inherit;
      border: 1px solid lightgray;
      max-height: 310px;
      z-index: 1;
      position: relative;
      overflow-y: scroll;
    }

    &__item {
      padding: 10px;
      text-overflow: ellipsis;
      overflow: hidden;
      white-space: nowrap;
      cursor: pointer;
      transition: 0.3s ease-out;

      &:hover {
        background: bisque;
      }

      &:last-child {
        margin-bottom: 0px;
      }
    }
  }

  .fade {
    &-enter,
    &-leave-to {
      opacity: 0;
    }

    &-enter-to,
    &-leave {
      opacity: 1;
    }

    &-enter-active,
    &-leave-active {
      transition: opacity 0.3s ease;
    }
  }
</style>
