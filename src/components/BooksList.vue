<template>
  <BookWrapper>
    <Book
      v-for="book in books"
      :key="book.id"
      :id="book.id"
      :title="book.title"
      :author="book.author"
      :description="book.description"
    />
  </BookWrapper>
</template>

<script>
import Book from './Book.vue';
import BookWrapper from './BookWrapper.vue';

export default {
  props: ['books'],
  components: { Book, BookWrapper },
};
</script>
