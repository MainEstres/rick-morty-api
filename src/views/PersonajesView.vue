<script setup>
import { useGetData } from '@/composable/getData';
import { computed, nextTick, onMounted, ref, watchEffect } from 'vue';
const { data, error, loading, getData } = useGetData();

const page = ref(1)
getData(`https://rickandmortyapi.com/api/character/?page=${page.value}`);
const collection = ref([]);

watchEffect(() => {
    if (data.value && data.value.results) {
        collection.value = [...collection.value, ...data.value.results];
    }
});

const stepLoad = ref(20);

const collectionLoaded = computed(() =>
    collection.value.slice(0, stepLoad.value)
);

const lastItemRef = ref(null)


const observer = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
        if (entry.isIntersecting) {
            console.log('Intersecting 👋');
            page.value++;
            getData(`https://rickandmortyapi.com/api/character/?page=${page.value}`);
        }
    });
});


const startObserving = async () => {
    await nextTick();
    if (lastItemRef.value) {
        observer.observe(lastItemRef.value);
    }
};

onMounted(() => {
    startObserving();
});



const applyStatus = (personaje) => {
    if (personaje.status === 'Alive') {
        return 'bg-green-400';
    } else if (personaje.status === 'Dead') {
        return 'bg-red-600';
    } return 'bg-gray-300'

};



</script>


<template>

    <h1 class="text-center text-4xl pt-36 font-bold">Personajes de Rick y Morty</h1>

    <p class="pt-30 text-center" v-if="loading">Cargando personajes...</p>
    <p class="pt-36" v-if="error">{{ error }}</p>

    <div v-if="data" class="flex flex-wrap justify-center gap-4 pt-8">+


        <div v-for="(item, index) in collectionLoaded" :key="item.id"
            :ref="index === collectionLoaded.length - 1 ? lastItemRef : null" class="w-64 p-4 border rounded-lg">
            <h2 class="text-center font-semibold">{{ item.name }}</h2>
            <p class="text-center" :class="applyStatus(item)">{{ item.status }}</p>
            <img :src="item.image" alt="Character image" class="w-full h-auto rounded-lg" />
        </div>




        <!-- <div class="bg-slate-400 rounded-xl p-4 flex flex-col" v-for="personaje in data.results" :key="personaje.id">
            <img class="rounded-full my-1 w-3/4 self-center " :src="personaje.image" :alt="personaje.name">
            <h1 class=" text-lg  font-bold">{{ personaje.name }}</h1>
            <div class="flex items-center gap-1">
                <div :class="applyStatus(personaje)" class="rounded-full h-3 w-3"></div>
                <h4 class="font-medium">{{ personaje.status }} - {{ personaje.species }}</h4>
            </div>
            <h4>{{ personaje.location.name }}</h4>

        </div> -->


    </div>


</template>
