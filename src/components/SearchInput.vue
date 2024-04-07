<template>
  <div class="search">
    <button
      class="search__button"
      type='button'
      @click="onSearch"
    >
      <search-icon />
    </button>
    <input
      class="search__input"
      v-model="computedModel"
      :type="type"
      :placeholder="placeholder"
      @keyup="onInput"
    />
  </div>
</template>

<script setup>
import { computed, defineProps, defineEmits } from 'vue';
import SearchIcon from '../assets/SearchIcon.vue'

const props = defineProps({
  modelValue: {
    type: String,
    default: "",
  },
  type: {
    type: String,
    default: "text",
  },
  placeholder: {
    type: String,
    default: "Filter by autor...",
  },
});
const emit = defineEmits(["update:modelValue", "search"]);

const computedModel = computed({
  set (val) {
    emit("update:modelValue", val);
  },
  get () {
    return props.modelValue;
  },
});


const onInput = (event) => {
  if (event.keyCode == 13) {
    onSearch()
    return
  }
  computedModel.value = event?.target?.value;
};
const onSearch = () => {
  emit("search");
}
</script>

<style scoped lang='scss'>
.search {
  display: flex;
  align-items: stretch;
  width: fit-content;
  background-color: white;
  overflow: hidden;
  border-radius: 0.25rem;
  border: 1px solid #c6c9cd;

  &__button {
    height: inherit;
    padding: 0 5px;
    opacity: 0.3;
    transition: all 0.2s linear;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    &:hover {
      opacity: 1;
    }
    background-color: transparent;
    border: none;
  }
  &__input {
    border: none;
    border-left: 1px solid #c6c9cd;
    outline: #c6c9cd;
    padding: 5px 10px;
    &::placeholder {
      color: #c6c9cd;
    }
  }
}
</style>