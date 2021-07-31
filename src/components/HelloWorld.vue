<template>
  <v-container>
    <v-row class="text-center">
     <v-col>
       <v-btn @click="getUserData">Load data</v-btn>
       <p>{{ myName }}</p>
       <ul v-for="user in userData" :key="user._id">
         <li>{{user.firstName}}  
           <v-btn @click="deleteAUser(user)">Delete</v-btn>
           <v-btn @click="updateData(user)">update</v-btn>
           </li>
       </ul>
     </v-col>
     <v-col>

      <v-text-field v-model="dataToPost.firstName"/><br/>
      <v-text-field v-model="dataToPost.email"/><br/>
      <v-text-field v-model="dataToPost.phone"/><br/>

      <v-btn @click="postData">Post A User</v-btn>
     </v-col>
      <hr>
      <v-col>
       <v-text-field v-model="dataToUpdate.firstName"/><br/>
      <v-text-field v-model="dataToUpdate.email"/><br/>
      <v-text-field v-model="dataToUpdate.phone"/><br/>
      <v-btn @click="sendUpdate">Update A User</v-btn>
     </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from 'axios'
export default {
  name: 'HelloWorld',
  data: () => ({
    myName: "isaac",
    userData: [],
    dataToPost: {
      firstName: '',
      email: '',
      phone: ''
    },
    defaultPost: {
       firstName: '',
      email: '',
      phone: ''
    },
    dataToUpdate: {
      _id: "",
        firstName: '',
      email: '',
      phone: ''
    },
    editedIndex: -1
  }),
  methods: {
    async getUserData(){
      try {
        const response = await axios.get("http://localhost:3000/users");
        this.userData = response.data;
      } catch (error) {
        console.log(error);
      }
    },
    async postData(){
      try {
        const response = await axios.post("http://localhost:3000/users", this.dataToPost);
        this.userData.push(response.data);
        this.dataToPost = Object.assign({}, this.defaultPost);
      } catch (error) {
        console.log(error);
      }
    },
    async updateData(item){
      // try {
        this.editedIndex = this.userData.indexOf(item)
        this.dataToUpdate = Object.assign({}, this.userData[ this.editedIndex])
    },
    async sendUpdate(){
      try {

       const response = await axios.put(`http://localhost:3000/users/${this.dataToUpdate._id}`, this.dataToUpdate);
       if(response.status === 200 || response.status === 201){
          this.userData.splice(this.editedIndex, 1, response.data);
          this.dataToUpdate = Object.assign({}, this.defaultPost);
          // splice(a, b, c)
        }       
      } catch (error) {
        console.log(error);
      }
    },
    async deleteAUser(item){
      try {
        this.editedIndex = this.userData.indexOf(item)
        const idToDelete = this.userData[this.editedIndex]._id
        const response = await axios.delete(`http://localhost:3000/users/${idToDelete}`)
        if(response.status === 200 || response.status === 201){
          this.userData.splice(this.editedIndex, 1);
          // splice(a, b, c)
        }
      } catch (error) {
        console.log(error);
      }
    }
  }
}
</script>
