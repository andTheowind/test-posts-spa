<script>
import { ref, onMounted, computed } from 'vue'
import UserFilter from './components/UserFilter.vue'
import PostList from './components/PostsList.vue'

export default {
  components: { UserFilter, PostList },
  setup() {
    const posts = ref([])
    const users = ref([])
    const searchQuery = ref('')
    const error = ref(null)

    const fetchPosts = async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts')
        if (!response.ok) throw new Error('Ошибка загрузки публикаций')
        posts.value = await response.json()
      } catch (err) {
        error.value = err.message
      }
    }

    const fetchUsers = async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/users')
        if (!response.ok) throw new Error('Ошибка загрузки авторов')
        users.value = await response.json()
      } catch (err) {
        error.value = err.message
      }
    }

    onMounted(async () => {
      await Promise.all([fetchPosts(), fetchUsers()])
    })

    const postsWithAuthors = computed(() => {
      if (!posts.value.length || !users.value.length) return [];
      let usersCache = {};

      return posts.value.reduce((result, post) => {
        if (!usersCache[post.userId]) {
          const user = users.value.find(u => u.id === post.userId);
          usersCache[post.userId] = user || {
            name: 'Неизвестный автор',
            username: ''
          };
        }
        result.push({
          ...post,
          authorName: usersCache[post.userId].name,
          authorUsername: usersCache[post.userId].username
        });

        return result;
      }, []);
    });

    const filteredPosts = computed(() => {
      if (!searchQuery.value) return postsWithAuthors.value

      const query = searchQuery.value.toLowerCase()
      return postsWithAuthors.value.filter(post =>
        post.authorName.toLowerCase().includes(query) ||
        post.authorUsername.toLowerCase().includes(query)
      )
    })

    const handleFilter = (query) => {
      searchQuery.value = query
    }

    return {
      posts,
      users,
      filteredPosts,
      handleFilter,
      error
    }
  }
}
</script>

<template>
  <div class="app-container">
    <div class="app-main">
      <div class="container">
        <UserFilter :users="users" @filter="handleFilter" />
        <PostList :posts="filteredPosts" :error="error" />
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.app-main {
  flex: 1;
  margin-top: 1em;
}
</style>
