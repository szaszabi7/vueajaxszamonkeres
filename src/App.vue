<template>
  <div class="container">
    <div class="d-flex justify-content-center mt-4">
      <h1 class="text-white ">Statues</h1>
    </div>
    <div id="app" class="d-flex justify-content-center mt-1">
      <table class="table table-dark table-striped">
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
              <button @click="deleteStatue(statue.id)" class="btn btn-danger me-1">Törlés</button>
              <button @click="editStatue(statue.id)" class="btn btn-info">Szerkesztés</button>
            </td>
          </tr>
          <tr>
            <td>
              <input type="hidden" v-model="statue.id">
              <input type="text" placeholder="Név" class="form-control" v-model="statue.person">
            </td>
            <td>
              <input type="number" placeholder="Magasság" class="form-control" v-model="statue.height">
            </td>
            <td>
              <input type="number" placeholder="Ár" class="form-control" v-model="statue.price">
            </td>
            <td>
              <button v-if="mod_new" @click="newStatue" :disabled="saving" class="btn btn-success me-1">Létrehoz</button>
              <button v-if="mod_new" @click="cancelEdit" :disabled="saving" class="btn btn-danger">Mégse</button>
              <button v-if="!mod_new" @click="saveStatue" :disabled="saving" class="btn btn-success me-1">Mentés</button>
              <button v-if="!mod_new" @click="cancelEdit" :disabled="saving" class="btn btn-danger">Mégse</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
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
    async deleteStatue(id) {
      let Response = await fetch(`http://127.0.0.1:8000/api/statues/${id}`, {
        method: 'DELETE'
      })
      console.log(Response)
      await this.loadStatue()
    },
    async editStatue(id) {
      let Response = await fetch(`http://127.0.0.1:8000/api/statues/${id}`)
      let data = await Response.json()
      this.statue = {...data};
      this.mod_new = false
    },
    async saveStatue() {
      this.saving='disabled'
     await fetch(`http://127.0.0.1:8000/api/statues/${this.statue.id}`, {
       method: 'PATCH',
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
    cancelEdit () {
      this.resetForm()
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
