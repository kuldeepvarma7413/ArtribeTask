<script setup>
import axios from 'axios';
    const props=defineProps({
        title: String,
        nextCardIndex: Number,
        container_id: Number,
        color: String
    })
    // store cards
    const cards=ref([])

    const addCard=async ()=>{
        try{
            const index = await axios.get(`http://localhost:4000/idx`).then(res=> res.data[0].index)
            CreateCard(index)
            axios.put(`http://localhost:4000/idx/1`,{index: index+1})
        }catch(error){
            console.log("Error fetching data")
        }
    }
    // update card details
    const CreateCard=(cardId)=>{
        // demo info of card
        const card={id: cardId.toString(), title: "", description:"", status:"", container_id: props.container_id}
        cards.value.push(card)
        // add card in db
        axios.post(`http://localhost:4000/cards/`, card).then(res=>{
            console.log("Card created successfully.");
        }).catch(error=>{
            console.log("Error occured");
        })
    }
    // get all cards
    const getCards=()=>{
        
        axios.get(`http://localhost:4000/cards?container_id=${props.container_id}`).then(res=>{
            const allCards=res.data;
            allCards.forEach(element => {
                cards.value.push(element)
            });
        }).catch(error=>{
            console.log("Error occured");
        })
    }
    const startDrag=(event, card)=>{
        event.dataTransfer.dropEffect='move'
        event.dataTransfer.effectAllowed='move'
        event.dataTransfer.setData('cardIndex', card.id)
    }
    const onDrop= async (event, containerId)=>{
        const CardIdx=event.dataTransfer.getData('cardIndex');
        try{
            var card = await axios.get(`http://localhost:4000/cards/${CardIdx}`).then(res=> res.data);
            // check we are not dropping in same container
            if(card.container_id != containerId){
                // change container id
                card.container_id=containerId
                // update cards array
                cards.value.splice(card.id, 1, card)
                // update in database
                await axios.put(`http://localhost:4000/cards/${CardIdx}`, card).then(res=> console.log)
                console.log("updated")
            }else{
                console.log("Same container")
            }
        }catch(error){
            console.log("Error occured while updating container id or fetching data")
        }
    }
    getCards();
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
                <a @click="addCard">+</a>
            </div>
        </div>
        <div class="cards-container" @drop="onDrop($event, props.container_id)" @dragenter.prevent @dragover.prevent>
            <Card v-for="card in cards" 
                :key="card.cardIndex" 
                :cardIndex="card.id" 
                :description="card.description" 
                :cardtitle="card.cardtitle" 
                :status="card.status" 
                draggable="true" @dragstart="startDrag($event, card)"/>

            <a @click.prevent="addCard" class="new card">
                <p>+</p>
                <p>New</p>
            </a>
        </div>

    </div>
</template>

<style>
    *{
        padding: 0;    
        margin: 0;
    }
    body{
        padding: 50px;
    }
    a{
        text-decoration: none;
        color: gray;
    }
    .container{
        width: 280px;
        padding: 10px 8px;
        margin: 10px 20px;
    }
    .container-header{
        width: 100%;
        display: flex;
        justify-content: space-between;
        padding: 8px 0px;
    }
    .left-side, .right-side{
        display: flex;
        align-items: center;
        gap: 10px;
    }
    .right-side{
        font-size: 22px;
        font-weight: 600;
    }
    #containerHeading{
        padding: 2px 5px;
        border-radius: 5px;
    }
    .new{
        display: flex;
        gap: 10px;
        color: gray;
        font-weight: 400;
        box-shadow: none;
    }
    .new, .right-side a{
        cursor: pointer;
    }
    .cards-container{
        height: 70vh;
        overflow-x: auto;
        padding: 5px;
    }
</style>