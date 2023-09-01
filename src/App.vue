<script setup>
import axios from 'axios';
import { reactive, onMounted } from 'vue'
// import {useToast} from 'vue-toast-notification'

import { toast } from 'vue3-toastify';
import 'vue3-toastify/dist/index.css';

axios.defaults.baseURL = "https://basic-blog.teamrabbil.com/api"

// const toast = useToast()

const appData = reactive({
    appname: "MK-BLOG",
    isMenuOpen: false,
    isLoading: false,
    catagories: [],
    curCatagory: 0,
    curPosition: 0,
    post: {
    },
    posts: [],
    recentPosts: [],
    page_mode: "post" // "list"
})

const fetch_blog_detail = async () => {
    appData.isLoading = true
    const resp = await axios.get("/post-details/" + appData.curPosition)
    appData.post = resp.data['postDetails']
    if (resp.data["postDetails"] == null) {
        // alert("no data found")
        toast.error('This post does not have any more details!!', { autoClose: 1500, });

    } else {
        appData.page_mode = "post"
    }
    appData.isLoading = false
}
const page_init = async () => {

    appData.isLoading = true
    let resp = await axios.get("/post-categories")
    appData.catagories = resp.data

    resp = await axios.get("/post-newest")
    // appData.curCatagory = resp.data[0]['category_id']
    appData.recentPosts = resp.data
    appData.curCatagory = 0
    appData.curPosition = resp.data[0]['id']
    appData.isLoading = false

    await fetch_blog_detail()


}
onMounted(async () => {

    appData.isLoading = true
    appData.page_mode = ""
    await page_init()
    appData.page_mode = "post"

})

const list_posts = async (id) => {
    appData.isLoading = true
    const resp = await axios.get("/post-list/" + id)
    appData.posts = resp.data
    appData.page_mode = 'list'

    appData.curCatagory = id
    appData.isLoading = false
}

const show_post = async (id) => {
    // alert(id)
    appData.curPosition = id
    await fetch_blog_detail()
}
function topFunction() {
    document.body.scrollTop = 0; // For Safari
    document.documentElement.scrollTop = 0; // For Chrome, Firefox, IE and Opera
}

const formatDate = (inputString) => {
    const options = { year: 'numeric', month: 'short', day: 'numeric', hour: 'numeric', minute: 'numeric', second: 'numeric' };
    return new Date(inputString).toLocaleDateString(undefined, options);
}

</script>


<template>
    <div class="h-screen" @click="appData.isMenuOpen = false">
        <div class="flex flex-col md:flex-row justify-between items-start p-3 border-2">
            <div class="flex justify-between items-center w-full">
                <span class="text-sm font-bold">
                    <a href="/" @click.prevent="page_init()" class="flex flex-row justify-center items-center space-x-2">
                        <img class="w-6 h-auto rounded-full" src="/logo_lwmk.png" alt="{{appData.appname}}">
                        <span>MK BLOG</span>
                    </a>
                </span>
                <div role="status" v-show="appData.isLoading">
                    <svg aria-hidden="true"
                        class="inline w-8 h-8 mr-2 text-gray-200 animate-spin dark:text-gray-600 fill-gray-600"
                        viewBox="0 0 100 101" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <path
                            d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
                            fill="currentColor" />
                        <path
                            d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
                            fill="currentFill" />
                    </svg>
                    <span class="sr-only">Loading...</span>
                </div>
                <div className="block lg:hidden">
                    <button @click.stop="appData.isMenuOpen = !appData.isMenuOpen"
                        className="flex items-center px-3 py-2 rounded text-black-500 hover:text-black-400">
                        <svg class="fill-current h-3 w-3 block" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg">
                            <path d="M0 3h20v2H0V3zm0 6h20v2H0V9zm0 6h20v2H0v-2z" />
                        </svg>
                    </button>
                </div>
            </div>
            <!-- {{ appData.curCatagory }} -->
            <nav class="mx-10 md:block w-full" :class="appData.isMenuOpen ? 'block' : 'hidden'">
                <ul
                    class="flex flex-col w-full md:flex-row justify-start space-x-5 md:justify-center items-center bg-blue-300 md:bg-white">
                    <li class="cursor-pointer px-2 shadow-gray-400 shadow-lg hover:bg-gray-800 hover:text-white  hover:rounded-md"
                        @click.prevent.stop="page_init()"
                        :class="appData.curCatagory == 0 ? 'bg-slate-900 text-white' : ''">
                        হোম
                    </li>
                    <li @click.prevent="list_posts(catagory.id); appData.isMenuOpen = false"
                        class="cursor-pointer hover:bg-gray-800 hover:text-white hover:rounded-md whitespace-nowrap"
                        :class="appData.curCatagory == catagory.id ? 'bg-slate-900 text-white px-2' : ''"
                        v-for="catagory, index in appData.catagories" :key="index">
                        {{ catagory.name }}
                    </li>
                </ul>
            </nav>
        </div>
        <template v-if="appData.page_mode == 'list'">
            <div class="grid grid-cols-2 mt-5 md:grid-cols-3 md:m-20">

                <div v-for="post, index in appData.posts" :key="index"
                    class="flex flex-col justify-center items-center border-2 m-5">
                    <!-- {{ post }} -->
                    <img class="rounded-lg" :src="post.img" :alt="post.title">
                    <div class="m-5 flex flex-col justify-center items-start">
                        <span class="text-sm font-bold mt-2">{{ post.title }}</span>
                        <p class="mt-2 text-sm">
                            {{ post.short }}
                        </p>
                    </div>
                    <div class="flex justify-between items-center w-full pl-5 mb-4">
                        <span>ID: {{ post.id }}</span>
                        <button class="cursor-pointer bg-blue-700 text-white border-2 rounded-lg px-2 py-1 mr-5"
                            @click.prevent="show_post(post.id); topFunction();">
                            more
                        </button>
                    </div>
                </div>
            </div>
        </template>
        <div v-if="appData.page_mode == 'post'" class="flex flex-col justify-center items-center mt-5">
            <!-- {{ appData.post }} -->
            <span class="text-xs text-gray-600 italic my-2">
                Last Updated:
                {{
                    formatDate(appData.post.updated_at)
                }}</span>
            <img class="rounded-lg max-w-sm  md:max-w-5xl" :src="appData.post.img" :alt="appData.post.title">
            <div class="flex flex-col justify-center items-start  max-w-sm  md:max-w-5xl border-2 p-5">
                <span class="text-lg font-bold">{{ appData.post.title }}</span>
                <p class="mt-2 text-sm">
                    {{ appData.post.content }}
                </p>
            </div>
        </div>

        <div v-if="appData.page_mode == 'post'" class="flex flex-col justify-center items-center">
            <h1 class="text-md font-bold mt-10 underline underline-offset-8">সাম্প্রতিক তালিকা</h1>
            <div class="grid grid-cols-2 mt-5 md:grid-cols-6 md:mt-5 md:mx-20">
                <div v-for="post, index in appData.recentPosts" :key="index"
                    @click.prevent.stop="show_post(post.id); topFunction();"
                    class="relative flex flex-col justify-center items-center border-2 m-5 cursor-pointer hover:ring-4 hover:shadow-xl hover:bg-blue-200">
                    <!-- {{ post }} -->
                    <img class="rounded-sm" :src="post.img" :alt="post.title">
                    <div class="m-5 flex flex-col justify-center items-start">
                        <span class="text-xs font-bold mt-2">{{ post.title }}</span>
                        <p class="mt-2 text-xs">
                            {{ post.short }}
                        </p>
                    </div>
                    <span
                        class="text-gray-200 font-bold right-2 top-2 absolute border-2 p-1 rounded-full text-xs border-gray-400 hover:bg-gray-600">{{
                            post.id }}</span>
                </div>
            </div>

        </div>
        <div class="flex justify-center items-center m-10">
            <span class="text-xs italic">copyrights @ mkzaman.com | 2023</span>
        </div>
    </div>
</template>

<style scoped></style>
