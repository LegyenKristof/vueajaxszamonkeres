<template>
  <div id="app">
    <table class="table table-dark">
      <thead>
        <th>Személy</th>
        <th>Magasság</th>
        <th>Ár</th>
        <th>Műveletek</th>
      </thead>
      <tbody>
        <tr v-for="statue in statues" :key="statue.id">
          <td>{{statue.person}}</td>
          <td>{{statue.height}}</td>
          <td>{{statue.price}}</td>
          <td>
            <button @click="deleteStatue(statue.id)" :disabled="saving" class="btn-dark">Törlés</button>
            <button @click="editStatue(statue.id)" :disabled="saving" class="btn-dark">Szerkesztés</button>
          </td>
        </tr>
        <tr>
          <td><input type="text" v-model="statue.person"></td>
          <td><input type="number" v-model="statue.height"></td>
          <td><input type="number" v-model="statue.price"></td>
          <td>
            <button v-if="!edit" @click="createStatue" :disabled="saving" class="btn-dark">Hozzáad</button>
            <button v-if="edit" @click="saveStatue" :disabled="saving" class="btn-dark">Mentés</button>
            <button v-if="edit" @click="cancelEdit" :disabled="saving" class="btn-dark">Mégse</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      statues: [],
      statue: {
        id: null,
        person: "",
        height: null,
        price: null
      },
      edit: false,
      saving: false
    }
  },
  methods: {
    async listStatues(){
      let response = await fetch('http://127.0.0.1:8000/api/statues')
      let data = await response.json()
      this.statues = data
    },
    async createStatue(){
      this.saving = true
      await fetch('http://127.0.0.1:8000/api/statues', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json'
        },
        body: JSON.stringify(this.statue) 
      })
      await this.listStatues()
      this.saving=false
      this.resetForm()
    },
    async deleteStatue(id){
      await fetch(`http://127.0.0.1:8000/api/statues/${id}`, {
        method: 'DELETE',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json'
        }
      })
      await this.listStatues()
    },
    resetForm(){
      this.statue = {
        id: null,
        person: "",
        height: null,
        price: null
      }
      this.edit = false
    },
    async editStatue(id){
      this.edit = true
      let response = await fetch(`http://127.0.0.1:8000/api/statues/${id}`)
      let data = await response.json()
      this.statue = {...data}
    },
    cancelEdit(){
      this.resetForm()
    },
    async saveStatue(){
      this.saving = true
      await fetch(`http://127.0.0.1:8000/api/statues/${this.statue.id}`, {
        method: 'PATCH',
        headers: {
          'Content-Type' : 'application/json',
          'Accept' : 'application/json'
        },
        body: JSON.stringify(this.statue)
      })
      await this.listStatues()
      this.saving = false
      this.resetForm()
    }
  },
  mounted(){
    this.listStatues()
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  background-color: black;  
}
</style>
