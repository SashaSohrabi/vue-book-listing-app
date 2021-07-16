<template>
  <h1>{{ books }}</h1>
</template>

<script>
import gql from 'graphql-tag';
export default {
  name: 'mainApp',
  data() {
    return {
      books: '',
    };
  },
  apollo: {
    books: {
      query: gql`
        query {
          books {
            title
            author
            description
          }
        }
      `,
    },
  },
};
</script>

<style></style>
