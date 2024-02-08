<template>
    <div class="app">
        <h1>Страница с постами</h1>
        <MyInput 
            v-model="searchQuery"
            placeholder="Поиск...."
        />
        <div class="app__btns">
            <MyButton
                @click="showDialog"
            >
                Создать пост
            </MyButton>
            <MySelect 
                v-model="selectedSort"
                :options="sortOptions"
            />
        </div>
        <MyDialog v-model:show="dialogVisible">
            <PostForm
                @create="createPost"
            />
        </MyDialog>
        <PostList
            :posts="sortedAndSearchedposts"
            @remove="removePost"
            v-if="!isPostsLoading"
        />
        <div v-else>Идет загрузка...</div>
    </div>
</template>

<script>
import PostForm from "@/components/PostForm";
import PostList from "@/components/PostList";
import MyDialog from "@/components/UI/MyDialog";
import MyButton from "@/components/UI/MyButton";
import MySelect from "@/components/UI/MySelect";
import axios from 'axios'
import MyInput from "./components/UI/MyInput.vue";

export default{
    components: {
    PostForm, PostList, MyDialog, MyButton, MySelect,
    MyInput
},
    data(){
        return {
            posts: [],
            dialogVisible: false,
            isPostsLoading: false,
            selectedSort: '',
            searchQuery: '',
            sortOptions: [
                {value: 'title', name: 'По названию'},
                {value: 'body', name: 'По содержанию'},
            ]
        }
    },
    methods: {
        createPost(post){
            this.posts.push(post)
            this.dialogVisible = false
        },
        removePost(post){
            this.posts = this.posts.filter(p => p.id !==post.id)
        },
        showDialog() {
            this.dialogVisible = true
        },
        async fetchPost(){
            try {
                this.isPostsLoading = true;
                setTimeout( async()=>{
                    const response = await axios.get( 'https://jsonplaceholder.typicode.com/posts?_limit=10');
                    this.posts = response.data
                },1000)
            } catch (e) {
                alert('Ошибка')
            } finally {
                this.isPostsLoading = false;
            }
        },
    },
    mounted() {
        this.fetchPost();
    },
    computed: {
        sortedPosts(){
            return [...this.posts].sort((post1, post2) => {
                return post1[this.selectedSort]?.localeCompare(post2[this.selectedSort])
            })
        },
        sortedAndSearchedposts(){
            return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
        },
    },
}
</script>

<style>
*
{
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

.app
{
    padding: 20px;
}

.app__btns
{
    display: flex;
    justify-content: space-between;
    margin: 15px 0;
}
</style>