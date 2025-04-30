<template>
    <div class="saved-locations">
      <h2>Saved Locations</h2>
      <ul v-if="saved.length">
        <li v-for="(location, index) in saved" :key="index">
          <button @click="selectLocation(location)">
            {{ location }}
          </button>
          <span @click="removeLocation(index)" class="delete">✕</span>
        </li>
      </ul>
      <p v-else>No saved locations yet.</p>
    </div>
  </template>
  
  <script>
  export default {
    name: 'SavedLocations',
    props: ['onSelect'],
    data() {
      return {
        saved: []
      }
    },
    mounted() {
      const saved = localStorage.getItem('savedLocations')
      if (saved) {
        this.saved = JSON.parse(saved)
      }
    },
    methods: {
      selectLocation(location) {
        this.onSelect(location)
      },
      removeLocation(index) {
        this.saved.splice(index, 1)
        localStorage.setItem('savedLocations', JSON.stringify(this.saved))
      },
      addLocation(location) {
        if (!this.saved.includes(location)) {
          this.saved.push(location)
          localStorage.setItem('savedLocations', JSON.stringify(this.saved))
        }
      }
    }
  }
  </script>
  
  <style scoped>
  .saved-locations {
    margin: 40px auto;
    max-width: 600px;
    text-align: center;
    color: white;
  }
  
  ul {
    list-style: none;
    padding: 0;
  }
  
  li {
    display: inline-flex;
    align-items: center;
    gap: 8px; /* adjusts spacing with x mark */
    margin: 10px 0;
    background-color: rgba(255, 255, 255, 0.1);
    padding: 6px 12px;
    border-radius: 8px;
  }
  
  li:hover {
  background-color: rgba(255, 255, 255, 0.2);
}

  button {
    background: transparent;
    border: none;
    color: white;
    font-size: 18px;
    cursor: pointer;
  }
  
  .delete {
    color: red;
    font-size: 18px;
    cursor: pointer;
  }
  </style>
  