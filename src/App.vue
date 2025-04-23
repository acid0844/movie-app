<template>
  <div class="container">
    <h1>人気の映画一覧</h1>
    <input
      v-model="searchQuery"
      type="text"
      placeholder="映画タイトルで検索"
      class="search-input"
    />

    <div class="genre-filter">
      <button
        v-for="genre in genres"
        :key="genre.id"
        @click="selectedGenre = genre.id"
        :class="{ active: selectedGenre === genre.id }"
      >
        {{ genre.name }}
      </button>
      <button @click="selectedGenre = null" :class="{ active: selectedGenre === null }">
        すべて
      </button>
    </div>

    <!-- 🔄 ローディング表示 or 映画一覧 -->
    <div v-if="loading">読み込み中...</div>
    <div v-else class="movie-list">
      <div
        v-for="movie in filteredMovies"
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
      favorites: [],
      searchQuery: '',
      genres: [],
      selectedGenre: null
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

  .movie-list {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
    gap: 20px;

    @media screen and (max-width: 600px) {
      grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
      gap: 12px;
    }
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

    &:hover {
      transform: scale(1.03);
    }

    img {
      width: 100%;
      border-radius: 4px;

      @media screen and (max-width: 600px) {
        height: auto;
      }
    }

    button {
      margin-top: 10px;
      padding: 4px 8px;
      font-size: 0.9rem;
      border: none;
      border-radius: 6px;
      background-color: #ffd700;
      cursor: pointer;
    }

    @media screen and (max-width: 600px) {
      font-size: 0.9rem;
      padding: 8px;
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
