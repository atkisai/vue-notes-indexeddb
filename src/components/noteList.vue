<script>

export default {
    components:{},
  data(){
    return{
      todoList: [],
      db:'',
      selectedTodoId:'',
      selectedTodoIndex:'',
    }
  },
  created(){
    if(localStorage.getItem('id')!=null){localStorage.removeItem('id')}


  },
  mounted(){
    let openRequest = indexedDB.open("mystore", 5);
    openRequest.onupgradeneeded = function() {
      console.log('ok')
      let db = openRequest.result;
      if (!db.objectStoreNames.contains('notes')) {
        db.createObjectStore('notes', {keyPath: "id", autoIncrement: true});
      }
    };
    let t=this;
    openRequest.onsuccess = function () {
      t.db = openRequest.result;
      let tx = t.db.transaction("notes", "readonly");
      let store = tx.objectStore('notes');
      let req = store.getAll();
      req.onsuccess = function () {
        let allNotes = event.target.result;
        for (let i=0;i<allNotes.length;i++){
          let note = allNotes[i];
          t.todoList.push(note)
        }
      }
    }
  },
  methods:{
    createNote(){
      this.$emit('createNote')
    },
    addTodo(todo) {
      todo = this.inputField;
      this.todoList.push({name: todo, complete: false});
      this.inputField = '';
      console.log(this.todoList);
    },
    deleteTodo(id, index) {
      this.todoList.splice(index, 1);
      this.db.transaction(['notes'], 'readwrite').objectStore('notes').delete(id);
    },
    refactorTodo(id){
      this.$emit('createNote');
      localStorage.setItem('id', id)
    },
    toggle: function(item) {
      item.complete = !item.complete;
    },
    sendDeleteInfo(id, index){
      this.selectedTodoId = id;
      this.selectedTodoIndex = index;
    },
  },
  computed:{
  }
}
</script>

<template>
  <div>
    <nav class="nav fixed-top justify-content-center bg-light">
        <button @click="createNote" class="btn btn-outline-primary btn-lg mybtn">Add Note</button>
    </nav>
    <div class="container list">
      <div class="accordion" id="accordionExample">
        <div class="card" v-for="(todo, index) in todoList">

          <div class="card-header" id="headingOne">
            <h5 class="title" data-toggle="collapse" :data-target="'#index'+index" aria-expanded="false" :aria-controls="'index'+index">
              {{todo.title}}
            </h5>

            <span class="delete" data-toggle="modal" data-target="#deleteModal" @click="sendDeleteInfo(todo.id, index)">X</span>

            <!-- Modal -->
            <div class="modal fade" id="deleteModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
              <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                  <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">Delete note</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                      <span aria-hidden="true">&times;</span>
                    </button>
                  </div>
                  <div class="modal-body">A you sure want delete this note?</div>
                  <div class="modal-footer">
                    <button @click="deleteTodo(selectedTodoId, selectedTodoIndex)" type="button" class="btn btn-danger" data-dismiss="modal">Delete</button>
                  </div>
                </div>
              </div>
            </div>

            <span @click="refactorTodo(todo.id)" class="refactor">✎</span>

          </div>


          <div :id="'index'+index" class="collapse" aria-labelledby="headingOne" data-parent="#accordionExample">
            <div class="card-body">
              <ul class="list-group list-group-flush">

                <li v-for="item in todo.todoList" class="list-group-item" v-bind:class="{complete:item.complete}">
                  <del v-if="item.complete">
                     <h6>{{ item.name }}</h6>
                  </del>
                  <span v-else>
                     <h6>{{ item.name }}</h6>
                  </span>
                </li>

              </ul>
            </div>
          </div>

        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  a {padding: 22px}
  h1, h2 {font-weight: normal}
  h5 {margin-bottom: 0}
  ul {list-style-type: none;  padding: 0}
  li {
    display: inline-block;
    /*margin: 0 10px*/
  }
  a {color: #666666}

  .delete{color:pink; cursor:pointer}
  .delete:hover{color:red}

  .refactor{color:deepskyblue; cursor: pointer}
  .refactor:hover{color: blue}

  .list{margin-top: 50px}
  .title{cursor: pointer}

  .complete{background: #D4FDE5}
  .card-body{padding: 0}

</style>
