<template>
  <div class="container">
    <input
      v-model="query"
      @keyup.enter="fetchPlaces"
      placeholder="Search for a place..."
      :class="{ 'input-active': isInputActive, 'input-disabled': isDisabled }"
      @focus="isInputActive = true"
      @blur="isInputActive = false"
    />
    
    <button class="ml-10" @click="fetchPlaces" :disabled="isDisabled">Search</button>
    <div v-if="loading" class="spinner"></div>
    <table v-if="!loading && !error">
      <thead>
        <tr>
          <th>#</th>
          <th>Place Name</th>
          <th>Country</th>
        </tr>
      </thead>
      <tbody>
        <tr v-if="places.length === 0 && !error">
          <td colspan="3">Start searching</td>
        </tr>
        <tr v-for="(place, index) in places" :key="place.id">
          <td>{{ index + 1 }}</td>
          <td>{{ place.name }}</td>
          <td>
            <img
              :src="`https://flagsapi.com/${place.countryCode}/flat/32.png`"
              :alt="place.country"
            />
            {{ place.country }}
          </td>
        </tr>
      </tbody>
    </table>
    <div class="flex-class">
    <div v-if="!loading && !error && places.length > 0" class="pagination">
      <button @click="changePage('previous')">Previous</button>
      <button class="ml-10" @click="changePage('next')">Next</button>
    </div>
     <input
     
      type="number"
      v-model.number="itemsPerPage"
      min="1"
      max="10"
       @input="handleItemsPerPageChange"
      placeholder="Items per page"
      class="limit-input ml-10 pt-10"
    />
   </div>
      <div v-if="warningMessage" class="warning">{{ warningMessage }}</div>
  </div>
</template>

<script setup>
import { ref,onMounted,onUnmounted } from 'vue';
import axios from 'axios';

const query = ref('');
const places = ref([]);
const loading = ref(false);
const error = ref(null);
const page = ref(0);
const limit = ref(5);
const isDisabled = ref(false);
const isInputActive = ref(false);
const itemsPerPage = ref(5);
const warningMessage = ref('');
// Fetch places from the API
const fetchPlaces = async () => {
  if (!query.value.trim()) {
    places.value = [];
    return;
  }

  loading.value = true;
  isDisabled.value = true;
  error.value = null;

  try {
    const response = await axios.get('https://wft-geo-db.p.rapidapi.com/v1/geo/cities', {
      params: {
        offset: page.value * limit.value,
        limit: itemsPerPage.value,
        namePrefix: query.value
      },
      headers: {
        'x-rapidapi-key': import.meta.env.VITE_RAPIDAPI_KEY,
        'x-rapidapi-host': 'wft-geo-db.p.rapidapi.com'
      }
    });
    places.value = response.data.data;
    if (response.data.data.length === 0) {
      error.value = 'No result found';
    }
  } catch (err) {
    error.value = 'Error fetching data';
    console.error(err);
  } finally {
    loading.value = false;
    isDisabled.value = false;
  }
};

// Change page for pagination
const changePage = (direction) => {
  if (direction === 'next') {
    page.value++;
  } else if (direction === 'previous' && page.value > 0) {
    page.value--;
  }
  fetchPlaces();
};

// Focus search input with shortcut
const handleKeyDown = (event) => {
  if (event.key === '/' && (event.ctrlKey || event.metaKey)) {
    event.preventDefault();
    const searchInput = document.querySelector('input');
    if (searchInput) {
    searchInput.focus();
    fetchPlaces();
    }
  }
};


onMounted(() => {
  document.addEventListener('keydown', handleKeyDown);
});

onUnmounted(() => {
  document.removeEventListener('keydown', handleKeyDown);
});
const handleItemsPerPageChange = () => {
  if (itemsPerPage.value > 10) {
    warningMessage.value = 'Maximum items per page is 10. Setting to 10.';
    itemsPerPage.value = 10;
  } else {
    warningMessage.value = '';
  }
  fetchPlaces();
};
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: auto;
  padding: 20px;
}
input {
  width: 241px;
  height: 38px;
  font-size: 16px;
  background-color: rgb(255, 255, 255);
  border: 1px solid rgb(206, 212, 218);
  padding: 6px 12px;
  border-radius: 4px;
}
.input-active {
  border-color: #7952b3;
  box-shadow: 0 0 0 3px rgba(121, 82, 179, 0.25);
}
.input-disabled {
  background-color: rgb(233, 236, 239);
}
.spinner {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-radius: 50%;
  border-top: 4px solid #7952b3;
  width: 24px;
  height: 24px;
  animation: spin 1s linear infinite;
}
@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}
th, td {
  padding: 8px;
  border: 1px solid rgb(222, 226, 230);
}
th {
  font-weight: 700;
}
img {
  vertical-align: middle;
}
.pagination {
  margin-top: 10px;
}
.limit-input {
  width: 100px;
  height: 24px;
  font-size: 12px;
  border: 1px solid rgb(222, 226, 230);
  border-radius: 2px;
  padding-left: 4px;
  padding-right: 4px;
  margin-top: 10px;
}
.warning {
  color: #dc3545;
  font-size: 14px;
  margin-top: 10px;
}
.ml-10{
      margin-left: 10px;
}
.flex-class{
  display: flex;
}
.pt-10{
   padding-top: 10px;
}
</style>
