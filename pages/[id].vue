<script>
import axios from "axios";

export default {
  name: "CardUpdate",

  data() {
    return {
      cardId: "",
      card: {
        id: "",
        title: "",
        status: "",
        description: "",
      },
    };
  },

  mounted() {
    // fetch card id in url (params of route)
    this.cardId = this.$route.params.id;
    // declaring methods to be called when page gets refresh
    this.getCard(this.cardId);
  },

  methods: {
    async getCard(cardId) {
      // fetch card details
      try {
        await axios.get(`http://localhost:4000/cards/${this.cardId}`).then((res) => {
          this.card = res.data;
          // console.log(res.data)
        });
      } catch (error) {
        console.log(error);
      }
    },
    // update card details
    async updateCard(cardId) {
      await axios.put(`http://localhost:4000/cards/${this.cardId}`, this.card)
        .then((res) => {
          console.log("Card updated successfully.");
          this.$router.go(-1)
        })
        .catch((error) => {
          console.log("Error occured");
        });
    },
    // delete card
    async deleteCard(){
        await axios.delete(`http://localhost:4000/cards/${this.cardId}`)
        console.log("card deleted")
        //go back
        this.$router.go(-1)
    }
  },
};
</script>
<template>
  <div>
    <form @submit.prevent="updateCard" class="myForm">
      <p class="top-heading">Update Card</p>

      <label for="index">Card Number</label>
      <input type="text" id="index" v-model="card.id" disabled />

      <label for="title">Title</label>
      <input type="text" id="title" v-model="card.title" />

      <label for="status">Status</label>
      <input type="text" id="status" v-model="card.status" />

      <label for="description">Description</label>
      <textarea type="text" id="description" v-model="card.description"/>

      <div class="actions">
          <button @click.prevent="$router.go(-1)">Back</button>
          <button @click.prevent="deleteCard">Delete</button>
          <button type="submit">Update</button>
      </div>

    </form>
  </div>
</template>

<style>
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
