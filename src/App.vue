<template>
  <div id="app">

    <createNote v-if="state=='createNote'" @noteList="noteList"></createNote>
    <noteList v-if="state=='noteList'" @createNote="createNote"></noteList>
  </div>
</template>

<script>
import createNote from './components/createNote.vue'
import noteList from './components/noteList.vue'

export default {
  name: 'App',
  components: {createNote, noteList},
  created(){
    if(localStorage.getItem('i')==null){localStorage.setItem('i','0')}
  },
  mounted(){
//    let req = indexedDB.open("mystore", 4);
//    req.onupgradeneeded = function() {
//      let db = req.result;
//      if (!db.objectStoreNames.contains('notes')) {
//        db.createObjectStore('notes', {keyPath: "id", autoIncrement: true});
//      }
//    };
  },
  data(){
    return{
      state:'noteList',
    }
  },
  methods:{
    createNote(){
      this.state='createNote'
    },
    noteList(){
      this.state='noteList'
    },
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
  margin-top: 0;
}
</style>
