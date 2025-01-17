<script setup>
import { useGetData } from '@/composable/getData';
import { computed, nextTick, onMounted, ref } from 'vue';
const { data, error, loading, getData } = useGetData();

const page = ref(1)
const collection = ref([]);
const stepLoad = 20;
const loaded = ref(stepLoad)
console.log(collection.value);


const loadData = async () => {
    await getData(`https://rickandmortyapi.com/api/character/?page=${page.value}`);
    collection.value.push(...data.value.results)
}

const collectionLoaded = computed(() =>
    collection.value.slice(0, loaded.value)
);

const itemsRef = ref([])

const lastItemRef = computed(() => collection.value[loaded.value - 1]);

console.log(lastItemRef);

const observer = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
        if (entry.isIntersecting) {
            console.log('Intersecting 👋');
            page.value++;
            loadData()
            startObserving();
        }
    });
});


const startObserving = async () => {
    await nextTick();
    observer.observe(lastItemRef.value);

};

onMounted(() => {
    loadData()
    startObserving();
});


const statusData = {
    Alive: 'bg-green-400',
    Dead: 'bg-red-600',
    unknown: 'bg-gray-300'
}

</script>


<template>

    <h1 class="text-center text-4xl pt-36 font-bold">Personajes de Rick y Morty</h1>

    <p class="pt-30 text-center" v-if="loading">Cargando personajes...</p>
    <p class="pt-36" v-if="error">{{ error }}</p>

    <div v-if="data" class="flex flex-wrap justify-center gap-4 pt-8">+


        <div v-for="(item, index) in collectionLoaded" :key="index" ref="itemsRef" class="w-64 p-4 border rounded-lg">
            <h2 class="text-center font-semibold">{{ item.name }}</h2>
            <p class="text-center" :class="statusData[item.status]">{{ item.status }}</p>
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