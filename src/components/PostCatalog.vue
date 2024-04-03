<template>
  <div class="content">
    <div class="search-block">
      <search-input
        :model-value="searchText"
        @update:model-value="changeSearchText"
        @search="onSearch"
      />
    </div>
    <div class="result">
      <div
        v-if="!list.length"
        class="result__empty"
      >Result is empty</div>
      <div
        v-else
        class="result__list list"
      >
        <div
          class="list__item post"
          v-for="post in list"
          :key="post.id"
        >
          <div class="post__content">
            <div class="post__title">{{ post.heading }}</div>
            <div class="post__body">{{ post.description }}</div>
            <div class="post__autor">{{ post.autor }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref, computed, watch } from 'vue';
import SearchInput from './SearchInput.vue';

const list = ref([])
const users = ref([])
const listQuery = ref({})
const searchText = ref('')
const searchBlock = ref(true)

const query = computed(() => {
  if (!listQuery.value) {
    return ''
  }
  const queryList = []
  for (const [key, value] of Object.entries(listQuery.value)) {
    queryList.push(`${key}=${value}`);
  }
  if (!queryList.length) {
    return ''
  }
  return '?' + queryList.join(',')
})

onMounted(async () => {
  await updateUsers()
  updateList()
})

watch(
  () => searchText.value.length,
  (val, oldVal) => {
    if (val === 0 || (val > 2 && val !== oldVal)) {
      searchBlock.value = false
    }
  }
)

const updateUsers = async () => {
  try {
    users.value = await fetch('https://jsonplaceholder.typicode.com/users')
      .then((response) => response.json())
  } catch (error) {
    console.error(error)
    users.value = []
  }
}
const updateList = async () => {
  try {
    const currentList = await fetch('https://jsonplaceholder.typicode.com/posts' + query.value)
      .then((response) => response.json())
    if (!users.value.length) {
      list.value = []
    } else {
      list.value = currentList.map(el => {
        const currentUser = users.value.find(user => el.userId === user.id)
        const autor = !currentUser ? '' : currentUser.name || ''
        const heading = el.title.slice(0, 1).toUpperCase() + el.title.slice(1)
        const description = el.body.slice(0, 1).toUpperCase() + el.body.slice(1)
        return { ...{ autor, heading, description }, ...el }
      })
    }
  } catch (error) {
    console.error(error)
    list.value = []
  } finally {
    searchBlock.value = true
  }
}
const changeSearchText = (value) => {
  searchText.value = value
}
const onSearch = () => {
  if (searchBlock.value) { return }
  const lowerCaseText = searchText.value.toLowerCase()
  const currentUser = users.value.find(user => user.name.toLowerCase().includes(lowerCaseText))
  listQuery.value = !searchText.value ? {} : { userId: !currentUser ? undefined : currentUser.id }
  updateList()
}
</script>

<style
  scoped
  lang='scss'
>
* {
  box-sizing: border-box;
}
.content {
  background-color: #edf1f6;
  max-width: 1440px;
  padding: 40px;
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  gap: 20px;

  .search-block {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .result {
    &__empty {
      text-align: center;
      padding-top: 30px;
    }
    &__list {
      margin: -5px;
      display: flex;
      flex-wrap: wrap;
    }
  }
}
.post {
  padding: 5px;
  width: 33.33%;

  &__content {
    display: flex;
    flex-direction: column;
    gap: 10px;
    background-color: white;
    padding: 10px;
    height: 100%;
    border: 1px solid #eeeeee;
  }
  &__title {
    color: #6a99e0;
    font-weight: 600;
  }
  &__body {
    font-size: 14px;
    margin-top: auto;
  }
  &__autor {
    color: #c6c9cd;
    font-size: 12px;
  }
}
@media screen and (max-width: 1024px) {
  .content {
    padding: 40px 20px;
  }
  .post {
    width: 50%;
  }
}
@media screen and (max-width: 767px) {
  .content {
    padding: 40px 10px;
  }
  .post {
    width: 100%;
  }
}
@media screen and (max-width: 374px) {
  .content {
    padding: 40px 0px;

    .result__list {
      margin: 0;
    }
  }
  .post {
    width: 100%;
  }
}
</style>
