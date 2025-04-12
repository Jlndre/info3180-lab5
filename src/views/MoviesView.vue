<template>
  <div class="container">
    <h1 class="mb-4">Movies</h1>

    <div class="row">
      <div class="col-md-6 mb-4" v-for="movie in movies" :key="movie.id">
        <div class="card h-100">
          <div class="card-body p-0">
            <div class="row g-0">
              <div class="col-md-4">
                <img
                  :src="'/api/v1/posters/' + movie.poster"
                  class="img-fluid rounded-start movie-poster"
                  :alt="movie.title"
                />
              </div>
              <div class="col-md-8 p-3">
                <h5 class="card-title">{{ movie.title }}</h5>
                <p class="card-text">{{ movie.description }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="footer mt-5">
      <p>Copyright © {{ new Date().getFullYear() }} Flask Inc.</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const movies = ref([]);

const fetchMovies = async () => {
  try {
    const response = await fetch("/api/v1/movies");
    const data = await response.json();
    movies.value = data.movies;
  } catch (error) {
    console.error("Failed to fetch movies:", error);
  }
};

onMounted(() => {
  fetchMovies();
});
</script>

<style scoped>
.card {
  border-radius: 4px;
  overflow: hidden;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  height: 100%;
}

.movie-poster {
  height: 100%;
  object-fit: cover;
  width: 100%;
}

.card-title {
  font-weight: 600;
  margin-bottom: 8px;
}

.card-text {
  color: #555;
  font-size: 0.9rem;
  line-height: 1.5;
}

.footer {
  text-align: left;
  color: #666;
  font-size: 0.9rem;
}
</style>
