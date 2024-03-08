
import taskContainer from '~/components/taskContainer.vue';

<template>
    <div class="main-container">
        <taskContainer :title="'Not Started'" :container_id="1" :color="`#fca7b8`"/>
        <taskContainer :title="'In Progress'" :container_id="2" :color="`#fce3a7`"/>
        <taskContainer :title="'Completed'" :container_id="3" :color="`#a7fcd0`"/>
    </div>
</template>

<style>
    .main-container{
        display: flex;
        justify-content: center;
    }
    /* custom scrollbar */
    ::-webkit-scrollbar{
        width: 3px;
    }
    ::-webkit-scrollbar-thumb{
        background-color: gray;
        border-radius: 10px;
    }
</style>