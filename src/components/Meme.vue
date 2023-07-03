<script setup>
    import { onBeforeMount, onBeforeUpdate, ref } from 'vue';
    import HeartIcon from './HeartIcon.vue';

    const memes = ref([]); // Variable reactiva -> Cada que cambia se refresca en el DOM
    const favoritesMemes = ref([]);
    let allMemes = [];

    const getMemes = async () => {
        const response = await fetch('https://api.imgflip.com/get_memes');
        const { data } = await response.json();
        
        memes.value = data.memes
        allMemes = data.memes
    };

    const searchMeme = (event) => {
        memes.value = allMemes.filter((meme) => 
            meme.name.toLowerCase().includes(event.target.value.toLowerCase())
        );
    }

    const toggleFav = (memeId) => {
        const index = favoritesMemes.value.indexOf(memeId);
        (index > -1) 
            ? favoritesMemes.value.splice(index, 1) 
            : favoritesMemes.value.push(memeId);
    }

    getMemes();

</script>

<template>
    <h1 class="text-center">Meme search</h1>
    <section class="container d-flex flex-wrap">
        <input type="text" class="form-control mb-3" placeholder="Search meme" @input="searchMeme" />
        <div v-for="meme in memes" :key="meme.id" class="card d-flex flex-direction-column align-items-center rounded m-2">
            <HeartIcon 
                class="mb-1 favIcon" 
                :class="{ selected: favoritesMemes.includes(meme.id) }"
                @click="toggleFav(meme.id)"
            />
            <img :src="meme.url" class="img-thumbnail w-75" />
            <p>{{ meme.name }}</p>
        </div>
    </section>
</template>

<style scoped>
    .card{
        background-color: black;
        width: 23%;
    }

    .favIcon.selected {
        filter: invert(48%) sepia(79%) saturate(2476%) hue-rotate(86deg) brightness(118%) contrast(119%);
    }

    .favIcon:hover{
        cursor: pointer;
    }
</style>

