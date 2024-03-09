<template>
    <NuxtLink :to="`/${cardIndex}`">
        <div class="card" @click="updateCard">
            <p class="card-heading">Card {{ cardIndex }}</p>
        </div>
    </NuxtLink>
</template>

<script setup>
    const props=defineProps({
        cardIndex: String,
        cardtitle: String,
        description: String,
        status: String
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
}
.card p{
    cursor: pointer;
    width: fit-content;
}
</style>