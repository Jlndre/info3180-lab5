<template>
  <div>
    <h2>Add a Movie</h2>
    <form
      @submit.prevent="saveMovie"
      id="movieForm"
      enctype="multipart/form-data"
    >
      <div class="form-group mb-3">
        <label for="title" class="form-label">Movie Title</label>
        <input type="text" name="title" class="form-control" required />
      </div>

      <div class="form-group mb-3">
        <label for="description" class="form-label">Description</label>
        <textarea
          name="description"
          class="form-control"
          rows="3"
          required
        ></textarea>
      </div>

      <div class="form-group mb-3">
        <label for="poster" class="form-label">Movie Poster</label>
        <input
          type="file"
          name="poster"
          class="form-control"
          accept="image/*"
          required
        />
      </div>

      <button type="submit" class="btn btn-primary">Submit</button>
    </form>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

let csrf_token = ref("");

function getCsrfToken() {
  fetch("/api/v1/csrf-token")
    .then((response) => response.json())
    .then((data) => {
      csrf_token.value = data.csrf_token;
    });
}

function saveMovie() {
  const movieForm = document.getElementById("movieForm");
  const form_data = new FormData(movieForm);

  fetch("/api/v1/movies", {
    method: "POST",
    body: form_data,
    headers: {
      "X-CSRFToken": csrf_token.value,
    },
  })
    .then((response) => response.json())
    .then((data) => {
      console.log(data);
      alert(data.message || "Errors occurred. Check console.");
    })
    .catch((error) => {
      console.error(error);
    });
}

onMounted(() => {
  getCsrfToken();
});
</script>
