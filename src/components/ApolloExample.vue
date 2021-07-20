<template>
  <div class="container">
    <div class="col-md-12">
      <h1>Book listing APP With Vue.js Apollo and GraphQL</h1>
      <form>
        <div class="form-group">
          <input
            type="text"
            class="form-control"
            id="exampleFormControlInput1"
            placeholder="Title"
            v-model="title"
          />
        </div>
        <div class="form-group">
          <input
            type="text"
            class="form-control"
            id="exampleFormControlInput1"
            placeholder="Author"
            v-model="author"
          />
        </div>
        <div class="form-group">
          <textarea
            class="form-control"
            id="exampleFormControlTextarea1"
            rows="3"
            placeholder="Descrioption"
            v-model="description"
          >
          </textarea>
        </div>
        <button
          v-show="modifying"
          type="button"
          class="btn btn-info 
              btn-lg btn-block"
          @click="updateBook"
        >
          Save
        </button>
        <button
          type="button"
          class="btn btn-secondary 
              btn-lg btn-block"
          @click.prevent="createBook"
        >
          Create book
        </button>
      </form>
    </div>
    <div class="row">
      <div class="container mt-4">
        <div v-for="book in books" :key="book.id">
          <div class="col-md-12">
            <div>
              <div class="card">
                <div class="card-body">
                  Title: {{ book.title }}
                  <hr />
                  Author: {{ book.author }}, Description: {{ book.description }}
                </div>
                <div class="wrapper">
                  <button
                    class="btn btn-danger mt-3"
                    @click="removeBook(book.id)"
                  >
                    Delete
                  </button>
                  <button
                    class="btn btn-warning mt-3"
                    @click="
                      modifyBook(
                        book.id,
                        book.title,
                        book.author,
                        book.description
                      )
                    "
                  >
                    Modify
                  </button>
                </div>
              </div>
            </div>
          </div>
          <br />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import gql from 'graphql-tag';
import { v4 as uuidv4 } from 'uuid';

export default {
  data() {
    return {
      books: [],
      title: '',
      author: '',
      description: '',
      modifying: false,
      modifyingBookId: '',
    };
  },
  apollo: {
    books: {
      query: gql`
        query {
          books {
            id
            title
            author
            description
          }
        }
      `,
    },
  },

  methods: {
    createBook() {
      const id = uuidv4(); // Create uniq id

      if (this.title != '' && this.author != '' && this.description != '') {
        this.$apollo
          .mutate({
            mutation: gql`
              mutation createBook(
                $id: ID!
                $title: String!
                $author: String!
                $description: String!
              ) {
                createBook(
                  id: $id
                  title: $title
                  author: $author
                  description: $description
                ) {
                  id
                  title
                  author
                  description
                }
              }
            `,
            variables: {
              id: id,
              title: this.title,
              author: this.author,
              description: this.description,
            },
          })
          .then(response => {
            this.books.push(response.data.createBook);
            this.title = '';
            this.author = '';
            this.description = '';
          })
          .catch(error => console.log(error));
      } else {
        alert('Please fill all the fields');
      }
    },
    removeBook(id) {
      this.$apollo
        .mutate({
          mutation: gql`
            mutation removeBookById($id: ID!) {
              removeBookById(id: $id) {
                id
                title
                author
                description
              }
            }
          `,
          variables: {
            id: id,
          },
        })
        .then(response => {
          const {
            data: { removeBookById },
          } = response;
          this.books = [...removeBookById];
        })
        .catch(error => console.log(error));
    },
    modifyBook(id, title, author, description) {
      this.modifying = true;
      this.modifyingBookId = id;
      this.title = title;
      this.author = author;
      this.description = description;
    },
    updateBook() {
      this.$apollo
        .mutate({
          mutation: gql`
            mutation updateBook(
              $id: ID!
              $title: String!
              $author: String!
              $description: String!
            ) {
              updateBook(
                id: $id
                title: $title
                author: $author
                description: $description
              ) {
                id
                title
                author
                description
              }
            }
          `,
          variables: {
            id: this.modifyingBookId,
            title: this.title,
            author: this.author,
            description: this.description,
          },
        })
        .then(response => {
          const {
            data: { updateBook },
          } = response;

          this.books = [...updateBook];
          this.modifying = false;
          this.title = '';
          this.author = '';
          this.description = '';
          this.modifyingBookId = '';
        })
        .catch(error => console.log(error));
    },
  },
};
</script>

<style>
h1 {
  font-size: 30px;
}
.wrapper {
  display: flex;
  justify-content: space-between;
}
.wrapper button {
  width: 49.5%;
}
</style>
