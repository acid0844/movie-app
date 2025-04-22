<template>
  <div class="container">
    <h1>人気の映画一覧</h1>

    <!-- ⭐ 映画の詳細表示エリア -->
    <div v-if="selectedMovie" class="detail-card">
      <button @click="selectedMovie = null">閉じる</button>
      <img :src="getImageUrl(selectedMovie.poster_path)" />
      <h2>{{ selectedMovie.title }}</h2>
      <p>評価: {{ selectedMovie.vote_average }} / 10</p>
      <p>公開日: {{ selectedMovie.release_date }}</p>
      <p>{{ selectedMovie.overview }}</p>
    </div>

    <!-- 🔄 ローディング表示 or 映画一覧 -->
    <div v-if="loading">読み込み中...</div>
    <div v-else class="movie-list">
      <div
        v-for="movie in movies"
        :key="movie.id"
        class="movie-card"
        @click="showDetails(movie)"
      >
        <img :src="getImageUrl(movie.poster_path)" />
        <h2>{{ movie.title }}</h2>
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
      selectedMovie: null, // ⭐ クリックされた映画情報
      loading: true
    };
  },
  methods: {
    async fetchMovies() {
      try {
        const apiKey = "a1b8f3d5bac74979614e7949b1462394";
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
    },
    showDetails(movie) {
      this.selectedMovie = movie;
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
h1 {
  text-align: center;
  margin-bottom: 20px;
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
  cursor: pointer;
  transition: transform 0.2s ease;
}
.movie-card:hover {
  transform: scale(1.03);
}
.movie-card img {
  width: 100%;
  border-radius: 4px;
}

/* ⭐ 詳細エリア */
.detail-card {
  background: #f9f9f9;
  border: 2px solid #aaa;
  padding: 20px;
  margin-bottom: 30px;
  border-radius: 12px;
  position: relative;
}

/* 回り込みを解除（clearfix） */
.detail-card::after {
  content: "";
  display: block;
  clear: both;
}

.detail-card img {
  width: 200px;
  float: left;
  margin-right: 20px;
  border-radius: 8px;
}

.detail-card button {
  float: right;
  padding: 6px 12px;
}
</style>
