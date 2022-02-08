<template>
  <div id="app">
    <table>
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
            <button>Törlés</button>
            <button>Szerkesztés</button>
          </td>
        </tr>
        <tr>
          <td><input type="text" v-model="statue.person"></td>
          <td><input type="number" v-model="statue.height"></td>
          <td><input type="number" v-model="statue.price"></td>
          <td>
            <button v-if="!edit" @click="createStatue" :disabled="saving">Hozzáad</button>
            <button v-if="edit">Mentés</button>
            <button v-if="edit">Mégse</button>
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
    resetForm(){
      this.statue = {
        id: null,
        person: "",
        height: null,
        price: null
      }
      this.edit = false
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
}
</style>
