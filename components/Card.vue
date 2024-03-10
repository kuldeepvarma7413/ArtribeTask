<template>
    <NuxtLink :to="`/${card.id}`">
        <div class="card" :card="card">
            <p class="card-heading">Card {{ card.id }}</p>
        </div>
    </NuxtLink>
</template>

<script setup>
    const props=defineProps({
        card: Object
    });

</script>

<style>
.card{
    padding: 10px;
    border-radius: 5px;
    box-shadow: 0px 0px 5px #c9c9c9;
    margin: 10px 0px;
    font-weight: 500;
    cursor: move;
    transition: transform 0.2s ease-in-out;
    border-top: 3px solid rgb(98, 190, 255);
    padding-top: 7px;
}
.card p{
    cursor: pointer;
    width: fit-content;
}
</style>