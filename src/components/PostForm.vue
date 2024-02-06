<template>
    <form @submit.prevent>
        <h4>создание поста</h4>
        <input
            v-bind:value="title"
            @input="title = $event.target.value"
            class="input" type="text"
            placeholder="Название"
        >
        <input
            v-bind:value="body"
            @input="body = $event.target.value"
            class="input" type="text"
            placeholder="Описание"
        >
        <button
            class="btn"
            @click="createPost"
        >
            создать
        </button>
    </form>
</template>

<script>

</script>

<style>

</style>