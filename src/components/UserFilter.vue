<script>
import { ref, computed } from 'vue'

export default {
    props: {
        users: {
            type: Array,
            required: true
        }
    },
    emits: ['filter'],
    setup(props, { emit }) {
        const searchQuery = ref('')
        const showSuggestions = ref(false)

        const filteredUsers = computed(() => {
            if (!searchQuery.value) return []
            const query = searchQuery.value.toLowerCase()
            return props.users.filter(user =>
                user.name.toLowerCase().includes(query) ||
                user.username.toLowerCase().includes(query)
            )
        })

        const triggerSearch = () => {
            showSuggestions.value = false
            emit('filter', searchQuery.value)
        }

        const selectUser = (user) => {
            searchQuery.value = user.name
            triggerSearch()
        }
        const updateSearch = () => {
            showSuggestions.value = !!searchQuery.value
        }

        return {
            searchQuery,
            showSuggestions,
            filteredUsers,
            triggerSearch,
            selectUser,
            updateSearch
        }
    }
}
</script>

<template>
    <div class="user-filter">
        <div class="input-group">
            <button class="btn border-light" type="button" @click="triggerSearch">
                <img src="./../assets/loupe-search.svg" class="img-fluid opacity-50" alt="Search">
            </button>
            <input type="text" class="form-control" placeholder="Введите имя автора..." v-model="searchQuery"
                @input="updateSearch" @keyup.enter="triggerSearch" />
        </div>
        <div class="suggestions" v-if="showSuggestions && filteredUsers.length">
            <div class="suggestion-item p-2 border-bottom" v-for="user in filteredUsers" :key="user.id"
                @click="selectUser(user)">
                {{ user.name }}
            </div>
        </div>
    </div>
</template>


<style lang="scss" scoped>
.user-filter {
    display: flex;
    justify-content: center;
    position: relative;
    max-width: 428px;
    margin-left: auto;
    margin-right: auto;
}

.suggestions {
    position: absolute;
    width: 100%;
    max-height: 200px;
    overflow-y: auto;
    background: white;
    border: 1px solid #ddd;
    border-radius: 4px;
    z-index: 1000;
    margin-top: 2.4rem;

    .suggestion-item {
        cursor: pointer;
    }
}

.input-group {
    .btn {
        padding: 0.375em 0.75em;
        border-color: #ccc !important;
    }

    .btn.border-light {
        max-width: 42px;
        border-right: 0;
    }
}
</style>