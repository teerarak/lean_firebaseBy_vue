<template>
  <div id="app">
    <img src="./assets/logo.png">

    <div>
      <input type="text" v-model="newDataTable.inData">
      <button type="button" @click="addToDataTable">insert</button>
    </div>

    <br>

    <h1>Data ที่มีทั้งหมด อิอิ</h1>

    <div v-for="showData in dataTable">
      <input v-if = "showData.statusEdit == true" type="text" v-model = "showData.inData">
      <button v-if = "showData.statusEdit == true" type="button" @click = "changeToData(showData.id, showData.inData)">change</button>
      <span v-else > {{ showData.inData }} </span>
      <button type="button" @click = "edit(showData.id, showData.inData)">edit</button>
      <button type="button" @click = "deleteToData(showData)">delete</button>

      <br><br>
    </div>

  </div>
</template>

<script>

import firebase from 'firebase'
// Initialize Firebase
var config = {
  apiKey: 'AIzaSyBusxZL9q0ppRm_Y4m5DYLngvaUSOpKk5M',
  authDomain: 'learn-firebase-28095.firebaseapp.com',
  databaseURL: 'https://learn-firebase-28095.firebaseio.com',
  storageBucket: 'learn-firebase-28095.appspot.com',
  messagingSenderId: '447843757889'
}
firebase.initializeApp(config)

var dataTable = firebase.database().ref('student')

export default {
  data () {
    return {
      newData: '',
      count: '',
      editData: false,
      dataTable: [],
      newDataTable: {
        inData: '',
        statusEdit: false
      }
    }
  },
  mounted () {
    let vm = this
    dataTable.on('child_added', function (snapshot) {
      var itemDataTable = snapshot.val()
      itemDataTable.id = snapshot.key
      vm.dataTable.push(itemDataTable)
    })
    dataTable.on('child_changed', function (snapshot) {
      var idDataTable = snapshot.key
      var dataTableFind = vm.dataTable.find(dataTable => dataTable.id === idDataTable)
      dataTableFind.inData = snapshot.val().inData
      dataTableFind.statusEdit = snapshot.val().statusEdit
      if (vm.newDataTable === idDataTable) {
        vm.newDataTable.inData = snapshot.val().inData
        vm.newDataTable.statusEdit = snapshot.val().statusEdit
      }
    })
    dataTable.on('child_removed', function (snapshot) {
      var idDataTable = snapshot.key
      vm.dataTable.splice(vm.dataTable.findIndex(dataTable => dataTable.id === idDataTable), 1)
    })
  },
  methods: {
    addToDataTable () {
      dataTable.push(this.newDataTable)
  //    this.newDataTable.id = result.key
      this.newDataTable.inData = ''
    },
    edit (id, data) {
      var vm = this
      firebase.database().ref('student/' + id).update({
        statusEdit: vm.dataTable.statusEdit = true
      })
      this.count = id
      this.newData = data
    },
    changeToData (id, data) {
      var vm = this
      firebase.database().ref('student/' + id).update({
        inData: vm.dataTable.inData = data,
        statusEdit: vm.dataTable.statusEdit = false
      })
      this.editData = false
    },
    deleteToData: function (showData) {
      firebase.database().ref('student/' + showData.id).remove()
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
