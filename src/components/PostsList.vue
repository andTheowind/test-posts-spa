<script>
export default {
    props: {
        posts: {
            type: Array,
            required: true
        },
        error: {
            type: String,
            default: null
        }
    }
}
</script>

<template>
    <div class="post-list mt-3 mt-xl-4">
        <div v-if="error" class="alert alert-danger mb-4">
            {{ error }}
        </div>
        <div v-else>
            <div v-if="!posts.length" class="alert alert-info">
                Постов нет
            </div>

            <div v-else class="row g-4">
                <div v-for="post in posts" :key="post.id" class="col-md-6 col-lg-4">
                    <div class="card h-100">
                        <div class="card-header bg-transparent border-0 py-0">
                            <h2 class="card-title fw-bold h5 text-primary py-0">{{ post.title }}</h2>
                        </div>
                        <div class="card-body py-1 py-xl-2">
                            <p class="card-text">{{ post.body }}</p>
                        </div>
                        <div class="card-footer bg-transparent border-0 py-0">
                            <small class="text-muted opacity-75">{{ post.authorName }}</small>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>


<style lang="scss" scoped>
.card {
    padding: 1.5em;

    &-header {
        @media (min-width: 1400px) {
            min-height: 52.5px;
        }
    }

    &-header,
    &-body,
    &-footer {
        padding-inline: 0;
    }

    &-title,
    &-text {
        &::first-letter {
            text-transform: uppercase;
        }
    }

    &-title {

        &.h5,
        &h5 {
            font-size: 1.15rem;
        }
    }


    @media (max-width: 768px) {
        padding: 1em 1.25em;
    }

    @media (max-width: 576px) {
        padding: 1em 1.15em;
    }

}
</style>