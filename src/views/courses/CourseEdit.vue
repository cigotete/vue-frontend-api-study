<template>
  <h1>Edit a Course</h1>
  <div v-if="this.user.id == this.auth.user.id">
    <form @submit.prevent="updateCourse">
      <div>
        <label for="title">Course Title</label>
        <br>
        <input v-model="course.title" type="text" id="title" name="title" placeholder="Course Title">
      </div>
      <br>
      <div>
        <label for="description">Course Description</label>
        <br>
        <textarea v-model="course.description" id="description" cols="30" rows="10" placeholder="Course Description"></textarea>
      </div>
      <br>
      <div>
        <label for="category">Category</label>
        <br>
        <select id="category" v-model="course.category_id">
          <option value="" selected disabled>Select a Category</option>
          <option v-for="category in categories" :key="'category-' + category.id" :value="category.id">
            {{ category.name }}
          </option>
        </select>
      </div>
      <br>
      <div>
        <button type="submit">Edit Course</button>
      </div>
    </form>
  </div>
</template>

<script>

import { mapState } from 'vuex';

export default {
  data() {
    return {
      categories: [],
      course: {},
      user: {}
    }
  },

  computed: {
    ...mapState(['auth'])
  },

  created() {
    this.getCategories();
    this.getCourse();
  },

  methods: {
    getCategories() {
      this.axios.get('/categories')
        .then(response => {
          this.categories = response.data;
        })
        .catch(error => {
          console.log(error);
        });
    },
    getCourse() {
      this.axios.get('/courses/' + this.$route.params.id + '?included=category,user')
        .then(response => {
          this.course = response.data;
          this.user = response.data.user;

          if (this.user.id != this.auth.user.id) {
            this.$router.push({name: 'coursesList'})
          }
        })
        .catch(error => {
          console.log(error);
        });
    },
    updateCourse() {
      this.axios.put('/courses/' + this.$route.params.id, this.course)
        .then(() => {
          this.$router.push({name: 'courseDetails', params: {id: this.$route.params.id}});
        })
        .catch(error => {
          console.log(error);
        });
    },
  },
}
</script>

<style>

</style>