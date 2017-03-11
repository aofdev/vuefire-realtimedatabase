<template>
  <div id="app" class="container">
    <div class="page-header">
        <h1>Vue.js 2 & Realtime Database with Firebase</h1>
    </div>
    <div class="panel panel-default">
      <div class="panel-heading">
        <h3>Add Book</h3>
      </div>
      <div class="panel-body">
        <form id="form" class="form-inline" v-on:submit.prevent="addBook">
          <div class="form-group">
            <label for="bookTitle">Title:</label>
            <input type="text" id="bookTitle" class="form-control" v-model="newBook.title">
          </div>
          <div class="form-group">
            <label for="bookAuthor">Author:</label>
            <input type="text" id="bookAuthor" class="form-control" v-model="newBook.author">
          </div>
          <div class="form-group">
            <label for="bookUrl">URL:</label>
            <input type="text" id="bookUrl" class="form-control" v-model="newBook.url">
          </div>
          <input type="hidden" id="bookKey">
            <input type="submit" class="btn btn-primary" value="Add Book">
            <input type="button" id="btnUp" class="btn btn-primary" value="Update Book" v-on:click="updatebtn()" disabled>
        </form>
      </div>
    </div>
    <div class="panel panel-default">
      <div class="panel-heading">
        <h3>Books Lists</h3>
      </div>
      <div class="panel-body">
        <table class="table table-striped">
          <thead class="thead-inverse">
            <tr>
              <th>Title</th>
              <th>Author</th>
              <th>Edit</th>
              <th>Delete</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="book in books">
              <td>
                <a v-bind:href="book.url">{{ book.title }}</a>
              </td>
              <td>{{ book.author }}</td>
              <td>
                <span class="glyphicon glyphicon-pencil" v-on:click="updateBook(book)"><span>
              </td>
              <td>
                <span class="glyphicon glyphicon-trash" v-on:click="removeBook(book)"><span>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>


  </div>

</template>
<script>
import Hello from './components/Hello'

import Firebase from 'firebase'

import toastr from 'toastr';

let config = {
   //firebase console
}

let app = Firebase.initializeApp(config);
let db = app.database();
let booksRef = db.ref('books');
export default {
  name: 'app',
  firebase:{
    books: booksRef
  },
  data (){
    return {
      newBook: {
        title:'',
        author:'',
        url:''
      }
    }
  },
  methods:{
    addBook: function () {
      const maplat = document.getElementById('getlat').value;
      const maplng = document.getElementById('getlng').value;
      mapRef.push({"lat":maplat,"lng":maplng});
      booksRef.push(this.newBook);
      this.newBook.title = '';
      this.newBook.author = '';
      this.newBook.url = '';
    },
    updateBook:function(book) {
      this.newBook.title = book.title;
      this.newBook.author = book.author;
      this.newBook.url = book.url;
      const btnUpdate = document.getElementById('btnUp');
      btnUpdate.disabled = false;
      const inHidden = document.getElementById('bookKey');
      inHidden.value = book['.key'];

    },
    updatebtn:function() {
      const btnUpdate = document.getElementById('btnUp');
      const getIdUpdate = document.getElementById('bookKey');
      booksRef.child(getIdUpdate.value).set(this.newBook);
      this.newBook.title = '';
      this.newBook.author = '';
      this.newBook.url = '';
      btnUpdate.disabled = true;
    },
    removeBook: function (book) {
      booksRef.child(book['.key']).remove();
      toastr.success("Book removed");
    }
  }

}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
