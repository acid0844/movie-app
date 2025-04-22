<template>
  <div class="container">
    <h1>人気の映画一覧</h1>

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
        <div class="card-content">
          <h2>{{ movie.title }}</h2>
          <button @click.stop="toggleFavorite(movie)">
            {{ isFavorite(movie) ? '★ お気に入り済み' : '☆ お気に入り追加' }}
          </button>
        </div>
      </div>
    </div>

    <!-- ⭐ モーダル -->
    <div v-if="selectedMovie" class="modal-overlay" @click.self="closeModal">
      <div class="modal-content">
        <button class="modal-close" @click="closeModal">×</button>
        <img :src="getImageUrl(selectedMovie.poster_path)" />
        <h2>{{ selectedMovie.title }}</h2>
        <p>評価: {{ selectedMovie.vote_average }} / 10</p>
        <p>公開日: {{ selectedMovie.release_date }}</p>
        <p>{{ selectedMovie.overview }}</p>
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
      loading: true,
      favorites: []
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
    },
    toggleFavorite(movie) {
      const exists = this.favorites.some(fav => fav.id === movie.id);
      if (exists) {
        this.favorites = this.favorites.filter(fav => fav.id !== movie.id);
      } else {
        this.favorites.push(movie);
      }
      localStorage.setItem("favorites", JSON.stringify(this.favorites));
    },
    isFavorite(movie) {
      return this.favorites.some(fav => fav.id === movie.id);
    },
    closeModal() {
      this.selectedMovie = null;
    }
  },
  mounted() {
    this.fetchMovies();
    const storedFavs = localStorage.getItem("favorites");
    if (storedFavs) {
      this.favorites = JSON.parse(storedFavs);
    }
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
  display: flex;
  flex-direction: column;
  justify-content: space-between;
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
.movie-card button {
  margin-top: 10px;
  padding: 4px 8px;
  font-size: 0.9rem;
  border: none;
  border-radius: 6px;
  background-color: #ffd700;
  cursor: pointer;
}
.card-content {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  flex: 1;
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

/* モーダルエリア */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}
.modal-content {
  background: #fff;
  padding: 24px;
  max-width: 600px;
  width: 90%;
  max-height: 80vh; /* 縦の制限を追加 */
  overflow-y: auto;  /* 縦にスクロールできるように */
  border-radius: 10px;
  position: relative;
}
.modal-content img {
  width: 100%;
  max-height: 300px; /* ← 画像の高さを制限 */
  object-fit: contain; /* ← はみ出さずに縮小 */
  border-radius: 6px;
  margin-bottom: 16px;
}
.modal-close {
  position: absolute;
  top: 8px;
  right: 12px;
  font-size: 24px;
  background: none;
  border: none;
  cursor: pointer;
}

@media screen and (max-width: 600px) {
  .movie-list {
    grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
    gap: 12px;
  }

  .movie-card {
    font-size: 0.9rem;
    padding: 8px;
  }

  .movie-card img {
    width: 100%;
    height: auto;
  }

  .detail-card {
    padding: 16px;
  }

  .detail-card img {
    width: 100%;
    float: none;
    margin: 0 0 12px 0;
  }

  .detail-card button {
    float: none;
    display: block;
    margin-bottom: 10px;
  }
}
</style>
