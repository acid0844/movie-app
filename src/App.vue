<template>
  <div class="container">
    <h1>人気の映画一覧</h1>
    <input
      v-model="searchQuery"
      type="text"
      placeholder="映画タイトルで検索"
      class="search-input"
    />

    <GenreFilter
      :genres="genres"
      :selectedGenre="selectedGenre"
      :selectGenre="(id) => selectedGenre = id"
    />

    <div class="favorite-toggle">
      <button @click="showFavoritesOnly = !showFavoritesOnly">
        {{ showFavoritesOnly ? '★ お気に入りのみ表示中（戻す）' : '☆ お気に入りだけ表示' }}
      </button>
    </div>

    <!-- 🔄 ローディング表示 or 映画一覧 -->
    <div v-if="loading">読み込み中...</div>
    <div v-else class="movie-list">
      <MovieCard
        v-for="movie in filteredMovies"
        :key="movie.id"
        :movie="movie"
        :image-url="getImageUrl(movie.poster_path)"
        :is-favorite="isFavorite"
        :toggle-favorite="toggleFavorite"
        :on-click-card="() => showDetails(movie)"
      />
    </div>

    <!-- ⭐ モーダル -->
    <MovieModal
      v-if="selectedMovie"
      :movie="selectedMovie"
      :image-url="getImageUrl(selectedMovie.poster_path)"
      :close-modal="closeModal"
    />
  </div>
</template>

<script>
import axios from "axios";
import MovieCard from "./components/MovieCard.vue";
import MovieModal from "./components/MovieModal.vue";
import GenreFilter from "./components/GenreFilter.vue";

export default {
  components: {
    MovieCard,
    MovieModal,
    GenreFilter
  },
  data() {
    return {
      movies: [],
      selectedMovie: null, // ⭐ クリックされた映画情報
      loading: true,
      favorites: [],
      searchQuery: '',
      genres: [],
      selectedGenre: null,
      showFavoritesOnly: false
    };
  },
  computed: {
    filteredMovies() {
      return this.movies
      .filter(movie =>
        movie.title.toLowerCase().includes(this.searchQuery.toLowerCase())
      )
      .filter(movie =>
        this.selectedGenre === null
          ? true
          : movie.genre_ids.includes(this.selectedGenre)
      )
      .filter(movie =>
        this.showFavoritesOnly
          ? this.favorites.some(fav => fav.id === movie.id)
          : true
      );
    }
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
    },
    async fetchGenres() {
      try {
        const apiKey = "a1b8f3d5bac74979614e7949b1462394";
        const res = await axios.get(
          `https://api.themoviedb.org/3/genre/movie/list?api_key=${apiKey}&language=ja`
        );
        this.genres = res.data.genres; // idとnameのリスト
      } catch (error) {
        console.error("ジャンルの取得に失敗しました", error);
      }
    }
  },
  async mounted() {
    this.fetchMovies();
    const storedFavs = localStorage.getItem("favorites");
    if (storedFavs) {
      this.favorites = JSON.parse(storedFavs);
    }
    await this.fetchGenres();
  }
};
</script>

<style lang="scss" scoped>
.container {
  max-width: 960px;
  margin: 0 auto;
  padding: 20px;

  h1 {
    text-align: center;
    margin-bottom: 20px;
  }

  .search-input {
    width: 100%;
    max-width: 400px;
    margin: 10px auto 20px;
    display: block;
    padding: 10px;
    font-size: 1rem;
    border-radius: 8px;
    border: 1px solid #ccc;
  }

  .genre-filter {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    justify-content: center;
    margin-bottom: 20px;

    button {
      padding: 6px 12px;
      border: 1px solid #ccc;
      border-radius: 6px;
      background: #eee;
      cursor: pointer;

      &.active {
        background-color: #333;
        color: #fff;
      }
    }
  }

  .favorite-toggle {
    text-align: center;
    margin-bottom: 20px;

    button {
      padding: 8px 16px;
      border-radius: 6px;
      background: #ffeaa7;
      border: 1px solid #ccc;
      cursor: pointer;
      font-weight: bold;
    }
  }

  .movie-list {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
    gap: 20px;

    @media screen and (max-width: 600px) {
      grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
      gap: 12px;
    }
  }

  .card-content {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    flex: 1;
  }

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

    .modal-content {
      background: #fff;
      padding: 24px;
      max-width: 600px;
      width: 90%;
      max-height: 80vh;
      overflow-y: auto;
      border-radius: 10px;
      position: relative;

      img {
        width: 100%;
        max-height: 300px;
        object-fit: contain;
        border-radius: 6px;
        margin-bottom: 16px;

        @media screen and (max-width: 600px) {
          max-height: 200px;
        }
      }

      .modal-close {
        position: absolute;
        top: 8px;
        right: 12px;
        font-size: 24px;
        background: none;
        border: none;
        cursor: pointer;

        @media screen and (max-width: 600px) {
          top: 4px;
          right: 8px;
        }
      }

      @media screen and (max-width: 600px) {
        padding: 16px;
      }
    }
  }

  .detail-card {
    background: #f9f9f9;
    border: 2px solid #aaa;
    padding: 20px;
    margin-bottom: 30px;
    border-radius: 12px;
    position: relative;

    @media screen and (max-width: 600px) {
      padding: 16px;
    }

    img {
      width: 200px;
      float: left;
      margin-right: 20px;
      border-radius: 8px;

      @media screen and (max-width: 600px) {
        width: 100%;
        float: none;
        margin: 0 0 12px 0;
      }
    }

    button {
      float: right;
      padding: 6px 12px;

      @media screen and (max-width: 600px) {
        float: none;
        display: block;
        margin-bottom: 10px;
      }
    }
  }
}
</style>
