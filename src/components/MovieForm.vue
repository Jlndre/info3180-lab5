<template>
  <form
    id="movieForm"
    @submit.prevent="saveMovie"
    enctype="multipart/form-data"
  >
    <div class="mb-3">
      <label for="title" class="form-label">Movie Title</label>
      <input
        v-model="formData.title"
        type="text"
        class="form-control"
        id="title"
        required
      />
    </div>

    <div class="mb-3">
      <label for="description" class="form-label">Description</label>
      <textarea
        v-model="formData.description"
        class="form-control"
        id="description"
        rows="3"
        required
      ></textarea>
    </div>

    <div class="mb-3">
      <label for="poster" class="form-label">Movie Poster</label>
      <input
        type="file"
        class="form-control"
        id="poster"
        ref="fileInput"
        accept="image/*"
        required
        @change="handleFileChange"
      />
    </div>

    <button type="submit" class="btn btn-primary">Submit</button>

    <div
      v-if="message"
      class="alert mt-3"
      :class="{ 'alert-success': success, 'alert-danger': !success }"
    >
      {{ message }}
    </div>
  </form>
</template>

<script setup>
import { ref, onMounted } from "vue";

const formData = ref({
  title: "",
  description: "",
  poster: null,
});

const fileInput = ref(null);
const message = ref("");
const success = ref(false);
const csrf_token = ref("");

const handleFileChange = (e) => {
  formData.value.poster = e.target.files[0];
};

const getCsrfToken = async () => {
  try {
    const response = await fetch("/api/v1/csrf-token");
    const data = await response.json();
    csrf_token.value = data.csrf_token;
  } catch (error) {
    console.error("Error fetching CSRF token:", error);
  }
};

const saveMovie = async () => {
  const form = new FormData();
  form.append("title", formData.value.title);
  form.append("description", formData.value.description);
  form.append("poster", formData.value.poster);

  try {
    const response = await fetch("/api/v1/movies", {
      method: "POST",
      body: form,
      headers: {
        "X-CSRFToken": csrf_token.value,
      },
    });

    const data = await response.json();

    if (response.ok) {
      success.value = true;
      message.value = "Movie added successfully!";
      // Clear form
      formData.value = { title: "", description: "", poster: null };
      if (fileInput.value) fileInput.value.value = "";
    } else {
      throw new Error(data.errors || "Failed to add movie");
    }
  } catch (error) {
    success.value = false;
    message.value = error.message;
  }
};

onMounted(() => {
  getCsrfToken();
});
</script>

<style scoped>
.alert-success {
  color: #0f5132;
  background-color: #d1e7dd;
  border-color: #badbcc;
}

.alert-danger {
  color: #842029;
  background-color: #f8d7da;
  border-color: #f5c2c7;
}
</style>
