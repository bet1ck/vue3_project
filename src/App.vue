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
        <div
            class="observer"
            ref="observer"
        >
        </div>
        <!-- <div class="page__wrapper">
            <div
                v-for="pageNumber in totalPages"
                :key="pageNumber"
                class="page"
                :class="{
                    'current-page': page === pageNumber
                }"
                @click="changePage(pageNumber)"
            >
                {{ pageNumber }}
            </div>
        </div> -->
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
            page: 1,
            limit: 10,
            totalPages: 0,
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
        // changePage(pageNumber){
        //     this.page = pageNumber
        //     this.fetchPost()
        // },
        async fetchPost(){
            try {
                this.isPostsLoading = true;
                setTimeout( async()=>{
                    const response = await axios.get( 'https://jsonplaceholder.typicode.com/posts',{
                        params: {
                            _page: this.page,
                            _limit: this.limit,
                        }
                    });
                    this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
                    this.posts = response.data
                },1000)
            } catch (e) {
                alert('Ошибка')
            } finally {
                this.isPostsLoading = false;
            }
        },
        async loadMorePosts(){
            try {
                this.page += 1;
                 async()=>{
                    const response = await axios.get( 'https://jsonplaceholder.typicode.com/posts',{
                        params: {
                            _page: this.page,
                            _limit: this.limit,
                        }
                    });
                    this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
                    this.posts = [...this.posts, ...response.data];
                }
            } catch (e) {
                alert('Ошибка')
            }
        },
    },
    mounted() {
        this.fetchPost();
        const options = {
            rootMargin: "0px",
            threshold: 1.0,
        };
        const callback = (entries, observer) => {
            if (entries[0].isIntersecting && this.page < this.totalPages) {
                this.loadMorePosts()
                console.log("gthtctr");
            }
        };
        const observer = new IntersectionObserver(callback, options);
        observer.observe(this.$refs.observer);
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
    watch: {
        // page(){
        //     this.fetchPost()
        // }
    }
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

.page__wrapper
{
    display: flex;
    margin-top: 15px;
}

.page
{
    border: 1px solid black;
    padding: 5px;
}

.current-page
{
    border: 2px solid green;
}

.observer
{
    height: 30px;
    background: green;
}
</style>