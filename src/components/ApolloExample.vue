<template>
  <Container>
    <Form
      :title="title"
      :author="author"
      :description="description"
      :modifying="modifying"
      @create-book="createBook"
      @update-book="updateBook"
    />
    <div v-if="$apollo.loading">Loading...</div>
    <BooksList v-else :books="books" />
  </Container>
</template>

<script>
import gql from 'graphql-tag';
import { v4 as uuidv4 } from 'uuid';
import BooksList from './BooksList.vue';
import Form from './Form.vue';
import Container from './UI/Container.vue';

export default {
  components: { BooksList, Form, Container },
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
  provide() {
    return {
      removeBook: this.removeBook,
      modifyBook: this.modifyBook,
    };
  },
  methods: {
    createBook(title, author, description) {
      const id = uuidv4(); // Create uniq id

      if (title != '' && author != '' && description != '') {
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
              title: title,
              author: author,
              description: description,
            },
          })
          .then(response => {
            this.books.push(response.data.createBook);
            this.clearInputs();
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
          this.clearInputs();
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
    updateBook(title, author, description) {
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
            title: title,
            author: author,
            description: description,
          },
        })
        .then(response => {
          const {
            data: { updateBook },
          } = response;

          this.books = [...updateBook];
          this.modifying = false;
          this.clearInputs();
        })
        .catch(error => console.log(error));
    },
    clearInputs() {
      this.title = '';
      this.author = '';
      this.description = '';
      this.modifyingBookId = '';
    },
  },
};
</script>

<style>
.wrapper {
  display: flex;
  justify-content: space-between;
}
.wrapper button {
  width: 49.5%;
}
</style>
