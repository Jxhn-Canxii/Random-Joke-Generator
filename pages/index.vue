<template>
    <div class="flex justify-center items-center min-h-screen">
        <UCard>
            <template #header>
                <h1 class="font-bold">Random Joke Generator</h1>
            </template>
            <div class="block space-y-4">
                <UDivider label="Joke" />
                <h3>{{ jokes.setup }}</h3>
                <UButton color="blue" v-if="!showPunchline" @click.prevent="showPunchlineImage()">{{ jokes.setup?.split(' ')[0] }} ?</UButton>
                <UDivider label="Punchline" />
                <h3 v-if="showPunchline" class="font-bold">{{ jokes.punchline }}</h3>
            </div>
            <div class="flex items-center justify-center mt-4">
                <img
                    v-if="showPunchline"
                    class="h-64 w-full rounded"
                    :src="memeImage"
                    alt="Avatar"
                />
            </div>
            <template #footer>
             <UButton @click.prevent="getRandomJokes()">Next Joke</UButton>
            </template>
        </UCard>
    </div>
</template>
<script setup>
import {ref,onMounted} from "vue";
import axios from "axios";

const jokes = ref([]);
const memeImage = ref([]);
const showPunchline = ref(false);
const getRandomJokes = async () => {
    try {
        showPunchline.value = false;
        const response = await axios.get('https://official-joke-api.appspot.com/random_joke');
        jokes.value = response.data;
    } catch (error) {
        console.log(error);
    }
}
const showPunchlineImage = async () => {
    await getRandomMeme();
    showPunchline.value = true;
}
const getRandomMeme = async () => {
    try {
        const response = await axios.get('https://api.imgflip.com/get_memes');
        
        // Get the list of memes
        const memes = response.data.data.memes;

        // Get a random index
        const randomIndex = Math.floor(Math.random() * memes.length);

        // Get the random meme object
        const randomMeme = memes[randomIndex];

        // Assuming you want to display the meme's URL
        console.log('Random Meme URL:', randomMeme.url);
        
        // You can assign this URL to a reactive variable if you're using Vue.js
        memeImage.value = randomMeme.url;
    } catch (error) {
        console.error('Error fetching memes:', error);
    }
};

onMounted(() => {
    getRandomJokes();
});
</script>