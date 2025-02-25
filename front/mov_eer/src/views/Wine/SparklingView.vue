<template>
  <div class="sparkling-wine-view">
    <h1>🍾 Sparkling 와인</h1>
    <div class="wine-intro">
      <img
        src="@/assets/SparklingWine.png"
        alt="Sparkling Wine"
        class="wine-image"
      />
      <div class="wine-description">
        <p>
          <strong>Sparkling 와인</strong>은 와인에 자연적으로 생성된 탄산가스를 포함하여
          입안에서 톡톡 터지는 신선함과 우아함을 선사합니다.
          샴페인(Champagne), 프로세코(Prosecco), 까바(Cava) 등이 대표적입니다.
        </p>
        <p>
          Sparkling 와인은 축하할 일이 있을 때, 혹은 로맨틱한 저녁을 보낼 때 완벽한 선택입니다.
          그 가벼운 버블은 기분을 한껏 고조시키며, 가벼운 해산물 요리나 디저트와 환상적인 조화를 이룹니다.
        </p>
        <p class="sparkling-fantasy-pairing">
          🧙‍♂️ Sparkling 와인은 Fantasy(판타지) 장르와 잘 어울립니다.
          버블처럼 펼쳐지는 환상의 세계를 Sparkling 와인 한 잔과 함께 탐험해 보세요.
        </p>
      </div>
    </div>

    <div class="wine-list">
      <h2>추천 Sparkling 와인 리스트</h2>
      <div class="wine-card-container">
        <div v-for="wine in wines" :key="wine.id" class="wine-card">
          <h3>{{ wine.name }}</h3>
          <p>{{ wine.description }}</p>
          <p><strong>대표 브랜드:</strong> {{ wine.representativeBrands }}</p>
          <p><strong>추천 안주:</strong> {{ wine.foodPairing }}</p>
          <p>
            <span>{{ wine.foodEmoji }}</span>
          </p>
        </div>
      </div>
    </div>

    <div class="movies-scroll">
      <h2>페어링 with Sparkling 🍾</h2>
      <div class="movie-card-container">
        <div
          v-for="movie in paginatedMovies"
          :key="movie.id"
          class="movie-card"
        >
          <RouterLink :to="{ name: 'MovieDetailView', params: { moviePk: movie.id } }">
            <img :src="getImageUrl(movie.poster_url)" class="movie-poster" alt="Movie Poster" />
          </RouterLink>
          <p class="movie-title">{{ movie.title }}</p>
        </div>
      </div>

      <!-- 페이지 네비게이션 -->
      <div class="pagination">
        <button @click="prevPage" :disabled="currentPage === 1">이전</button>
        <span> {{ currentPage }} / {{ totalPages }}</span>
        <button @click="nextPage" :disabled="currentPage === totalPages">다음</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { useLiquorStore } from "@/stores/liquor";
import { useMovieStore } from "@/stores/movie";
import { onMounted } from "vue";
import { RouterLink } from "vue-router";
import { ref, computed } from "vue";

const currentPage = ref(1);
const itemsPerPage = 30; // 한 페이지에 표시할 영화 개수

// 페이징된 영화 목록
const paginatedMovies = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  const end = start + itemsPerPage;
  return getWineMovies('Sparkling').slice(start, end); // Sparkling Wine 기준으로 변경
});

// 총 페이지 수 계산
const totalPages = computed(() => {
  return Math.ceil(getWineMovies('Sparkling').length / itemsPerPage);
});

// 페이지 네비게이션 함수
const prevPage = () => {
  if (currentPage.value > 1) {
    currentPage.value -= 1;
  }
};

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value += 1;
  }
};


const wines = [
  {
    id: 1,
    name: "Champagne",
    description: "프랑스 샹파뉴 지역에서 생산되는 전통적인 스파클링 와인.",
    representativeBrands: "Moët & Chandon, Veuve Clicquot",
    foodPairing: "굴, 훈제 연어",
    foodEmoji: "🦪🐟"
  },
  {
    id: 2,
    name: "Prosecco",
    description: "이탈리아에서 생산된 상쾌하고 과일 향이 돋보이는 스파클링 와인.",
    representativeBrands: "La Marca, Mionetto",
    foodPairing: "가벼운 디저트, 과일 샐러드",
    foodEmoji: "🍰🍓"
  },
  {
    id: 3,
    name: "Cava",
    description: "스페인에서 만들어진 고품질의 스파클링 와인.",
    representativeBrands: "Freixenet, Codorníu",
    foodPairing: "치즈 플래터, 타파스",
    foodEmoji: "🧀🍢"
  }
];

const liquorStore = useLiquorStore();
const movieStore = useMovieStore();

onMounted(() => {
  liquorStore.getWines();
  movieStore.getMovies();
  movieStore.getGenres();
});

const getWineMovies = (subtype) => {
  return movieStore.movies.filter((movie) => {
    return movie.genres.some((genreId) => {
      const genre = movieStore.genres.find((g) => g.id === genreId);
      return genre && genre.subtype === subtype;
    });
  });
};

const getImageUrl = (path) => {
  if (!path) {
    return "https://via.placeholder.com/300x450";
  }
  return `https://image.tmdb.org/t/p/w300${path}`;
};
</script>

<style scoped>

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
  gap: 10px;
}

.pagination button {
  padding: 8px 12px;
  background-color: #333333;
  color: #ffffff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.pagination button:hover {
  background-color: #ee9191;
}

.pagination button:disabled {
  background-color: #777777;
  cursor: not-allowed;
}

.pagination span {
  color: #ffffff;
  font-size: 16px;
}

/* Sparkling 와인 스타일 - 어두운 테마 */
.sparkling-wine-view {
  padding: 20px;
  background-color: #1a1a1a; /* 어두운 배경 */
  font-family: Arial, sans-serif;
}

h1 {
  text-align: center;
  color: #ffffff; /* 흰색 텍스트 */
  margin-bottom: 30px;
}

.wine-intro {
  display: flex;
  align-items: center;
  margin-bottom: 40px;
}

.wine-image {
  width: 300px;
  height: auto;
  margin-right: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(255, 255, 255, 0.1);
}

.wine-description {
  color: #cccccc; /* 연한 회색 */
  font-size: 16px;
  line-height: 1.8;
  flex: 1;
}

.sparkling-fantasy-pairing {
  margin-top: 20px;
  font-style: italic;
  color: #999999; /* 더 어두운 회색 */
  font-size: 15px;
}

.wine-list h2 {
  color: #ffffff; /* 흰색 텍스트 */
  margin-bottom: 20px;
}

.wine-card-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.wine-card {
  background-color: #333333; /* 어두운 카드 배경 */
  border-radius: 8px;
  padding: 20px;
  width: 20%;
  min-width: 250px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.4);
  transition: transform 0.2s, box-shadow 0.2s;
}

.wine-card:hover {
  transform: scale(1.02);
  box-shadow: 0 6px 10px rgba(0, 0, 0, 0.6);
}

h3 {
  color: #ffffff; /* 흰색 텍스트 */
  margin-bottom: 10px;
}

p {
  color: #cccccc; /* 연한 회색 텍스트 */
  margin: 5px 0;
  line-height: 1.6;
}

/* 영화 스타일 */
.movies-scroll {
  margin-top: 40px;
}

.movie-card-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.movie-card {
  width: 150px;
  text-align: center;
}

.movie-poster {
  width: 100%;
  height: auto;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
}

.movie-title {
  margin-top: 10px;
  font-size: 14px;
  color: #ffffff; /* 흰색 텍스트 */
}

h2{
  color:  #ffffff
}
</style>
