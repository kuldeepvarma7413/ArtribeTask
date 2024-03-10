
<script>
    import axios from 'axios';
    // const axios=require('axios');
    export  default{
        data(){
            return{
                cards: [],
                uniqueIndex: "",
                formData:{
                    title: "",
                    status: "Not Started",
                    description: "",
                    container_id: 0
                }
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
            createCard(){
                // console.log("contid",this.formData.title)
                const card={"id":(this.uniqueIndex++).toString(),
                        "title": this.formData.title,
                        "description": this.formData.description,
                        "status": this.formData.status,
                        "container_id": this.formData.container_id }
                this.cards.push(card)
                axios.post(`http://localhost:4000/cards`, card).then(()=>{console.log("Card moved")}).catch("Error creating card");
                // clear form data and hide popup
                this.resetForm();
                this.showAndHidePopup()
            },
            // show popup
            showAndHidePopup(container_id=0){
                document.getElementById('popup').classList.toggle('show');
                this.formData.container_id=container_id
            },
            // reset form data
            resetForm(){
                this.formData = {"title":"", "description":"", "status":"", "container_id":0}
            }
        },
    }
</script>

<template>
    <div class="main-container">
        <!-- all 3 containers -->
        <taskContainer :title="'Not Started'" :container_id="1" :color="`#fca7b8`" :cards="filtercards(1)" @onmove="this.moveCardToContainer" @create="this.showAndHidePopup"/>
        <taskContainer :title="'In Progress'" :container_id="2" :color="`#fce3a7`" :cards="filtercards(2)" @onmove="this.moveCardToContainer" @create="this.showAndHidePopup"/>
        <taskContainer :title="'Completed'" :container_id="3" :color="`#a7fcd0`" :cards="filtercards(3)" @onmove="this.moveCardToContainer" @create="this.showAndHidePopup"/>
    </div>
    
    <!-- popup to add new info -->
    <div class="popup" id="popup">
        <form @submit.prevent="createCard" class="myForm">
            <p class="top-heading">Add Card</p>

            <label for="title">Title</label>
            <input type="text" v-model="formData.title"/>

            <label for="status">Status</label>
            <input type="text" v-model="formData.status"/>

            <label for="description">Description</label>
            <textarea type="text" v-model="formData.description"/>

            <div class="actions">
                <button @click.prevent="showAndHidePopup">Back</button>
                <button type="submit">Add</button>
            </div>
        </form>
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
    /* popup */
    .popup{
        display: none;
        position: fixed;
        background-color: rgba(0,0,0,0.3);
        height: 100%;
        width: 100%;
        top: 0;
        left: 0;
        z-index: 9999;
        transition: ease-in-out 1s;
    }
    .popup form{
        background-color: white;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        
    }
    .popup.show{
        display: block;
    }
    .myForm {
        padding: 20px 40px;
        width: 400px;
        margin: auto;
        display: flex;
        flex-direction: column;
        box-shadow: 0px 0px 5px rgb(197, 197, 197);
        border-radius: 5px;
    }
    .myForm p {
       text-align: center;
        margin-bottom: 20px;
    }
    input , textarea{
        width: 92%;
        padding: 10px 15px;
        border-radius: 5px;
        border: none;
        outline: none;
        background-color: #f5f5f5;
        font-size: 16px;
    }
    label{
        text-align: left;
    }
    .top-heading{
        width: 100%;
        color: white;
        background-color:rgb(170, 170, 170);
        border-radius: 5px;
        padding: 10px 0px;
    }
    button{
        cursor: pointer;
        width: 100px;
        padding: 5px 20px;
        background-color: rgb(224,224,224);
        outline: none;
        border: none;
        border-radius: 15px;
    }
    .actions{
        margin-top: 30px;
        display: flex;
        justify-content: space-evenly;
    }
    #description{
        resize: none;
        min-height: 100px;
    }
</style>