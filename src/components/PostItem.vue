<template>
    <div class="post">
        <div>
            <div>{{ post.id }}</div>
            <div><strong>Название:</strong>{{ post.title }}</div>
            <div><strong>Название:</strong>{{ post.body }}</div>
        </div>
        <div class="post__btns">
            <MyButton
                @click="$emit('remove',post)"
            >
                удалить
            </MyButton>
        </div>
    </div>
</template>

<script>
import MyButton from "@/components/UI/MyButton"
export default {
    components:{
        MyButton
    },
    props: {
        post:{
            type: Object,
            required: true
        }
    }
}
</script>

<style>
.post
{
    margin-top: 15px;
    padding: 15px;
    border: 2px solid teal;
    display: flex;
    align-items: center;
    justify-content: space-between;
}
</style>