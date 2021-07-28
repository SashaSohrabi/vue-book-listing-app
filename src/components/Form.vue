<template>
  <FormWrapper>
    <div>
      <h1>Book listing APP With Vue.js Apollo and GraphQL</h1>
      <form>
        <div class="form-group">
          <input
            type="text"
            class="form-control"
            placeholder="Title"
            v-model="formTitle"
          />
        </div>
        <div class="form-group">
          <input
            type="text"
            class="form-control"
            placeholder="Author"
            v-model="formAuthor"
          />
        </div>
        <div class="form-group">
          <textarea
            class="form-control"
            rows="3"
            placeholder="Descrioption"
            v-model="formDescription"
          >
          </textarea>
        </div>
        <button
          v-show="modifying"
          type="button"
          class="btn btn-info 
              btn-lg btn-block"
          @click="$emit('update-book', formTitle, formAuthor, formDescription)"
        >
          Save
        </button>
        <button
          type="button"
          class="btn btn-secondary 
              btn-lg btn-block"
          @click.prevent="submitForm"
        >
          Create book
        </button>
      </form>
    </div>
  </FormWrapper>
</template>

<script>
import FormWrapper from './UI/FormWrapper.vue';
export default {
  components: {
    FormWrapper,
  },
  emits: ['update-book', 'create-book'],
  props: ['title', 'author', 'description', 'modifying'],
  data() {
    return {
      formTitle: '',
      formAuthor: '',
      formDescription: '',
    };
  },
  watch: {
    title: {
      immediate: true,
      handler(val) {
        this.formTitle = val;
      },
    },
    author: {
      immediate: true,
      handler(val) {
        this.formAuthor = val;
      },
    },
    description: {
      immediate: true,
      handler(val) {
        this.formDescription = val;
      },
    },
  },
  methods: {
    submitForm() {
      this.$emit(
        'create-book',
        this.formTitle,
        this.formAuthor,
        this.formDescription
      );
      this.formTitle = '';
      this.formAuthor = '';
      this.formDescription = '';
    },
  },
};
</script>
