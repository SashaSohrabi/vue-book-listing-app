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
          type="button"
          class="btn btn-info 
              btn-lg btn-block"
          @click.prevent="modifyBook"
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
                  Author: {{ book.author }}, Descroption: {{ book.description }}
                </div>
                <button
                  class="btn btn-danger mt-3"
                  @click="removeBook(book.id)"
                >
                  Delete
                </button>
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
          });
      } else {
        alert('Please fill all the fields');
      }
    },
    removeBook(id) {
      if (confirm('Are you sure?')) {
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
              data: {
                removeBookById: { id },
              },
            } = response;

            const modifiedBooks = this.books.filter(book => book.id !== id);
            this.books = [...modifiedBooks];
          });
      }
    },
    modifyBook() {
      console.log('hello');
    },
  },
};
</script>

<style></style>
