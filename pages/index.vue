
<script>
    import axios from 'axios';
    // const axios=require('axios');
    export  default{
        data(){
            return{
                cards: [],
                uniqueIndex: ""
            };
        },
        async mounted(){
            await this.fetchCards();
            this.getuniqueIndex();
        },
        methods:{
            async fetchCards(){
                try{
                    const res = await axios.get(`http://localhost:4000/cards`);
                    this.cards=res.data
                }catch{
                    console.log("error fetching data")
                }
            },
            // find Unique index
            getuniqueIndex(){
                let index=0;
                this.cards.forEach(card => {
                    if( parseInt(card.id) > index){
                        index=parseInt(card.id);
                    }
                });
                this.uniqueIndex=index+1;
            },
            // filter cards
            filtercards(containerId){
                return this.cards.filter(card=> card.container_id==containerId);
            }, 
            // change container_id
            moveCardToContainer(current_cnt_id, card_id){
                const card= this.cards.find(card=> card.id == card_id)
                if(!card) throw Error("Card not found")
                card.container_id=current_cnt_id
                axios.put(`http://localhost:4000/cards/${card.id}`, card).then(()=>{console.log("Card moved")}).catch("Error while dropping card")
            },
            // create card
            createCard(container_id){
                const card={"id":(this.uniqueIndex++).toString() ,"title": "", "description": "", "status": "", "container_id": container_id}
                this.cards.push(card)
                axios.post(`http://localhost:4000/cards`, card).then(()=>{console.log("Card moved")}).catch("Error creating card");
            }
        },
    }
</script>

<template>
    <div class="main-container">
        <!-- all 3 containers -->
        <taskContainer :title="'Not Started'" :container_id="1" :color="`#fca7b8`" :cards="filtercards(1)" @onmove="this.moveCardToContainer" @create="this.createCard"/>
        <taskContainer :title="'In Progress'" :container_id="2" :color="`#fce3a7`" :cards="filtercards(2)" @onmove="this.moveCardToContainer" @create="this.createCard"/>
        <taskContainer :title="'Completed'" :container_id="3" :color="`#a7fcd0`" :cards="filtercards(3)" @onmove="this.moveCardToContainer" @create="this.createCard"/>
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