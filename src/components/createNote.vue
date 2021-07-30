<script>

export default {
    components:{},
  data(){
    return{
      title:'',
      inputField: '',
      todoList: [],
      db:'',
      id: localStorage.getItem('id')!=null ? parseInt(localStorage.getItem('id')) : '',
    }
  },
  created(){
    let req = indexedDB.open("mystore", 5);
    let t = this;
    req.onsuccess = function () {
      t.db = req.result;
      if(t.id != ''){
        let req = t.db.transaction(['notes'], 'readwrite').objectStore('notes').get(t.id);
        req.onsuccess = function(){
          let note = event.target.result;
          t.title = note.title;
          t.todoList = note.todoList;
        };
      }
    };
  },
  mounted(){
  },
  methods:{
    saveNote: function () {
      let t = this;
      if(t.id==''){
        if (t.title !== '') {
          t.db.transaction(['notes'], 'readwrite').objectStore('notes').add({title:t.title,todoList:t.todoList});
        }
      }else {
        t.db.transaction(['notes'], 'readwrite').objectStore('notes').put({title:t.title,todoList:t.todoList,id:t.id});
      }
    },
    addTodo: function(todo) {
      todo = this.inputField;
      this.todoList.push({name: todo, complete: false});
      this.inputField = '';
   },
    deleteTodo(id, index) {
      let t = this;
      t.todoList.splice(index, 1);
      if(t.id!=''){
        t.db.transaction(['notes'], 'readwrite').objectStore('notes').put({title:t.title,todoList:t.todoList,id:t.id});
      }
    },
   toggle: function(todo) {
      todo.complete = !todo.complete;
   },
    backToList(){
      this.$emit('noteList')
    }
  },
  computed:{
  }
}
</script>

<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-sm-6">
          <button @click="backToList" class="btn btn-danger">Back</button>
        </div>
        <div class="col-sm-6">
          <button @click="saveNote" class="btn btn-success">Save</button>
        </div>
      </div>
    </div>

    <div class="container">
      <form>
        <div class="form-group">
          <label>Title</label>
          <input class="form-control" v-model="title" placeholder="Title is required">
        </div>
        <div class="form-group">
          <label>New Todo</label>
          <input v-model="inputField" v-on:keyup.enter="addTodo" class="form-control" placeholder="Click 'Enter' to add case">
        </div>
      </form>

      <div class="card">
        <ul class="list-group list-group-flush">
          <li class="list-group-item" v-for="(todo, index) in todoList">
            <del v-if="todo.complete">
               <h6>{{ todo.name }}</h6>
            </del>
            <span v-else>
               <h6>{{ todo.name }}</h6>
            </span>
            <input type="checkbox" v-on:change="toggle(todo)" v-bind:checked="todo.complete">
            <span @click="deleteTodo(todo, index)" class="delete">X</span>
          </li>
        </ul>
      </div>
    </div>

  </div>
</template>

<style scoped>
  .general {
    margin: auto;
  }

  .navlinks {
    color: white;
  }

  .navlinks:hover {
    background: #4a76a8;
    cursor: hand;
  }

  .select:hover {
    cursor: hand;
  }

  .icon-bar {
    background: white;
  }
  a{
    padding: 22px;
  }
  .navbar-header{
    margin-top: 10px;
    margin-bottom: -10px;
  }


  h1, h2 {
  font-weight: normal;
}

h5 {
   margin-bottom: 0px;
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

.delete {
   color: pink;
   cursor: pointer;
}

.delete:hover {
   color: red;
}
</style>
