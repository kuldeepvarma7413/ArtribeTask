<script setup>
const props = defineProps({
  title: String,
  container_id: Number,
  color: String,
  cards: Array,
});
const emit = defineEmits(["onmove", "create"]);
async function addCard() {
  // create card
  emit("create", props.container_id);
}
function startDrag(event, card) {
  event.dataTransfer.dropEffect = "move";
  event.dataTransfer.effectAllowed = "move";
  event.dataTransfer.setData("cardIndex", card.id);
}
async function onDrop(event, containerId) {
  const CardIdx = event.dataTransfer.getData("cardIndex");
  emit("onmove", containerId, CardIdx);
}
</script>
<template>
  <div class="container">
    <div class="container-header">
      <div class="left-side">
        <p id="containerHeading" :style="{ background: color }">{{ title }}</p>
        <p id="count">{{ cards.length }}</p>
      </div>
      <div class="right-side">
        <a title="menu" class="menu">···</a>
        <a @click="addCard" title="new card">+</a>
      </div>
    </div>
    <div class="cards-container" @drop="onDrop($event, container_id)" @dragenter.prevent @dragover.prevent >
      <Card
        v-for="card in cards"
        :cardIndex="card.id"
        :description="card.description"
        :cardtitle="card.cardtitle"
        :status="card.status"
        draggable="true"
        @dragstart="startDrag($event, card)"
      />

      <a @click.prevent="addCard" class="new card">
        <p>+</p>
        <p>New</p>
      </a>
    </div>
  </div>
</template>

<style>
* {
  padding: 0;
  margin: 0;
}
body {
  padding: 50px;
}
a {
  text-decoration: none;
  color: gray;
}
.container {
  width: 280px;
  padding: 10px 8px;
  margin: 10px 20px;
}
.container-header {
  width: 100%;
  display: flex;
  justify-content: space-between;
  padding: 8px 0px;
}
.left-side,
.right-side {
  display: flex;
  align-items: center;
  gap: 10px;
}
.right-side {
  font-size: 22px;
  font-weight: 600;
}
#containerHeading {
  padding: 2px 5px;
  border-radius: 5px;
}
.new {
  display: flex;
  gap: 10px;
  color: gray;
  font-weight: 400;
  box-shadow: none;
}
.new,
.right-side a {
  cursor: pointer;
}
.cards-container {
  height: 70vh;
  overflow-x: auto;
  padding: 5px;
}
</style>
