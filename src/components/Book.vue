<template>
  <div class="col-md-12 mb-3">
    <div>
      <div class="card bg-light">
        <div class="card-body">
          Title: {{ title }}
          <hr />
          Author: {{ author }}, Description: {{ description }}
        </div>
        <div class="wrapper">
          <button class="btn btn-danger mt-3" @click="removeBook(id)">
            Delete
          </button>
          <button
            class="btn btn-warning mt-3"
            @click="modifyBook(id, title, author, description)"
          >
            Modify
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  inject: ['removeBook', 'modifyBook'],
  props: ['id', 'title', 'author', 'description'],
  emits: ['remove-book', 'modify-book'],
};
</script>

<style></style>
