<template>
  <div class="container">
    <h1>人気の映画一覧</h1>
    <div v-if="loading">読み込み中...</div>
    <div v-else class="movie-list">
      <div v-for="movie in movies" :key="movie.id" class="movie-card">
        <img :src="getImageUrl(movie.poster_path)" alt="ポスター" />
        <h2>{{ movie.title }}</h2>
        <p>公開日: {{ movie.release_date }}</p>
        <p>{{ movie.overview.slice(0, 60) }}...</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      movies: [],
      loading: true
    };
  },
  methods: {
    async fetchMovies() {
      try {
        const apiKey = "a1b8f3d5bac74979614e7949b1462394"; // ← ← ← 🔑ここにTMDBのAPIキーを貼り付けて！
        const res = await axios.get(
          `https://api.themoviedb.org/3/movie/popular?api_key=${apiKey}&language=ja`
        );
        this.movies = res.data.results;
      } catch (error) {
        console.error("映画データの取得に失敗しました", error);
      } finally {
        this.loading = false;
      }
    },
    getImageUrl(path) {
      return `https://image.tmdb.org/t/p/w300${path}`;
    }
  },
  mounted() {
    this.fetchMovies();
  }
};
</script>

<style scoped>
.container {
  max-width: 960px;
  margin: 0 auto;
  padding: 20px;
}
.movie-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
  gap: 20px;
}
.movie-card {
  border: 1px solid #ccc;
  padding: 10px;
  background: #fff;
  border-radius: 8px;
}
.movie-card img {
  width: 100%;
  border-radius: 4px;
}
h1 {
  text-align: center;
  margin-bottom: 20px;
}
</style>
