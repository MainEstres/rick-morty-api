<script setup>
import { useGetData } from '@/composable/getData';
import { onMounted, ref } from 'vue';
const { data, error, loading, getData } = useGetData();

const collection = ref([])
console.log(collection.value);

const page = ref(1);

const loadPersonaje = async () => {
    await getData(`https://rickandmortyapi.com/api/character/?page=${page.value}`)

    collection.value.push(...data.value.results);
}

const loadMore = async () => {
    // Guardar la posición actual del scroll
    const scrollPosition = window.scrollY;

    page.value++;
    await getData(`https://rickandmortyapi.com/api/character/?page=${page.value}`);
    collection.value.push(...data.value.results);

    // Restaurar la posición del scroll
    window.scrollTo(0, scrollPosition);
};

onMounted(() => {
    loadPersonaje();
})


const statusData = {
    Alive: 'bg-green-400',
    Dead: 'bg-red-600',
    unknown: 'bg-gray-300'
}

</script>


<template>

    <h1 class="text-center text-4xl pt-36 font-bold">Personajes de Rick y Morty</h1>

    <p class="pt-30 text-center" v-if="loading">Cargando personajes...</p>
    <p class="pt-36" v-else-if="error">{{ error }}</p>

    <div v-else-if="collection" class="flex flex-wrap justify-center gap-4 pt-8">

        <transition-group name="fade" tag="div" class="flex flex-wrap justify-center gap-4 pt-8 transition-all">
            <div class="bg-slate-400 rounded-xl p-4 flex flex-col" v-for="personaje in collection" :key="personaje.id">
                <img class="rounded-full my-1 w-3/4 self-center " :src="personaje.image" :alt="personaje.name">
                <h1 class=" text-lg  font-bold">{{ personaje.name }}</h1>
                <div class="flex items-center gap-1">
                    <div :class="statusData[personaje.status]" class="rounded-full h-3 w-3"></div>
                    <h4 class="font-medium">{{ personaje.status }} - {{ personaje.species }}</h4>
                </div>
                <h4>{{ personaje.location.name }}</h4>
            </div>
        </transition-group>



    </div>
    <div class="flex justify-center p-6">
        <button class="px-4 py-2 bg-blue-500 text-white rounded-lg" @click="loadMore()">Más
            personajes...</button>
    </div>

</template>

<style scoped>
/* Estilos para la transición de los personajes */
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.5s ease;
}

.fade-enter,
.fade-leave-to {
    opacity: 0;
}
</style>