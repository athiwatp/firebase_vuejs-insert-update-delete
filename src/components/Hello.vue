<template>
  <div class='hello'>
    <img src="../assets/logo.png"> <br>
    {{msg}} <br>
    <div v-for="show in data">
      {{show.id}} <br>
      {{show.name}} <br>
      {{show.surname}} <br>
      {{show.number}} <br>
      <button @click="del(show.id)"> X </button>
      <button @click="edit(show.id)"> Edit </button>
      <hr>
    </div>
  </div>
</template>

<script>
import firebase from 'firebase'
var config = {
  apiKey: 'xxxxxxxxxxxxxxxxxxxx',
  authDomain: 'xxxxxxxxxxxxxxxxxxxx',
  databaseURL: 'xxxxxxxxxxxxxxxxxxxx',
  storageBucket: 'xxxxxxxxxxxxxxxxxxxx',
  messagingSenderId: 'xxxxxxxxxxxxxxxxxxxx'
}
firebase.initializeApp(config)
var db = firebase.database().ref('users')
// db.push({
//   name: '123',
//   surname: '123',
//   number: '11111'
// })
export default {
  name: 'hello',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      data: []
    }
  },
  mounted () {
    let vm = this
    db.on('child_added', function (data) {
      // console.log(data.key)
      // console.log(data.val())
      let item = data.val()
      item.id = data.key
      vm.data.push(item)
    })
    db.on('child_removed', function (data) {
      let id = data.key
      let index = vm.data.findIndex(item => item.id === id)
      vm.data.splice(index, 1)
    })
    db.on('child_changed', function (data) {
      let id = data.key
      let index = vm.data.findIndex(item => item.id === id)
      let item = data.val()
      item.id = data.key
      vm.data[index].id = item.id
      vm.data[index].name = item.name
      vm.data[index].surname = item.surname
      vm.data[index].number = item.number
      console.log('data update : ', vm.data[index])
    })
  },
  methods: {
    del (id) {
      console.log('id ', id)
      firebase.database().ref('users/' + id).remove()
    },
    edit (id) {
      firebase.database().ref('users/' + id).update({
        name: 'tak',
        surname: 'tak',
        number: '029110020'
      })
    }
  }
}
</script>

<!-- Add 'scoped' attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
