<template>
  <div id="app">
    <h1>Statues</h1>
    <table style="margin-left: auto; margin-right: auto;">
      <thead>
        <tr>
          <th>Személy</th>
          <th>Magasság</th>
          <th>Ár</th>
          <th>Műveletek</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="statue in statues" v-bind:key="statue.id">
          <td>{{ statue.person }}</td>
          <td>{{ statue.height }}</td>
          <td>{{ statue.price }}</td>
          <td>
            <button>Törlés</button>
            <button>Szerkesztés</button>
          </td>
        </tr>
        <tr>
          <td>
            <input type="hidden" v-model="statue.id">
            <input type="text" v-model="statue.person">
          </td>
          <td>
            <input type="number" v-model="statue.height">
          </td>
          <td>
            <input type="number" v-model="statue.price">
          </td>
          <td>
            <button v-if="mod_new" @click="newStatue" :disabled="saving">Létrehoz</button>
            <button v-if="!mod_new" @click="saveStatue" :disabled="saving">Mentés</button>
            <button v-if="!mod_new" @click="cancelEdit" :disabled="saving">Mégse</button>
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
      mod_new: true,
      saving: false,
      statue: {
        id: null,
        person: '',
        height: '',
        price: '',
      },
      statues: []
    }
  },
  methods: {
    async loadStatue() {
      let Response = await fetch('http://127.0.0.1:8000/api/statues')
      let data = await Response.json()
      this.statues = data
    },
    async newStatue() {
      this.saving='disabled'
     await fetch('http://127.0.0.1:8000/api/statues', {
       method: 'POST',
       headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
       },
       body: JSON.stringify(this.statue) 
     })
     await this.loadStatue()
     this.saving=false
     this.resetForm()
    },
    resetForm() {
      this.statue = {
        id: null,
        person: '',
        height: '',
        price: ''
      }
      this.mod_new = true
    },
  },
  mounted() {
    this.loadStatue()
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
