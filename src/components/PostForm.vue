<template>
    <form @submit.prevent>
        <h4>создание поста</h4>
        <MyInput
            v-model="post.title"
            placeholder="Название"
        />
        <MyInput
            v-model="post.body"
            placeholder="Описание"
        />
        <MyButton
            @click="createPost"
        >
            создать
        </MyButton>
    </form>
</template>

<script>
import MyButton from "@/components/UI/MyButton"
import MyInput from "@/components/UI/MyInput"
export default {
    components:{ 
        MyButton,
        MyInput
    },
    data() {
        return{
            post:{
                title: '',
                body: ''
            }
        }
    },
    methods:{
        createPost(){
            this.post.id = Date.now();
            this.$emit('create', this.post)
            this.post = {
                title: '',
                vody: ''
            }
        }
    }
}
</script>

<style scoped>
form
{
    display: flex;
    flex-direction: column;
}

</style>