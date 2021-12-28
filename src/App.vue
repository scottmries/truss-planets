<template>
  <div>
    <div v-if="loading">Loading&hellip;</div>
    <template v-else>
      <div v-if="error">{{ error }}</div>
      <div v-else>
        <table>
          <tr>
            <th>Name</th>
            <th>Climate</th>
            <th>Residents</th>
            <th>Terrains</th>
            <th>Population</th>
            <th>Surface area covered in water (km<sup>2</sup>)</th>
          </tr>
          <tr v-for="planet in planets" :key="planet.name">
            <td>
              <a :href="planet.url" target="_blank">{{ planet.name }}</a>
            </td>
            <td>{{ planet.climate }}</td>
            <td>{{ formatNumber(planet.residents.length) }}</td>
            <td>{{ planet.terrain }}</td>
            <td>{{ formatNumber(planet.population) }}</td>
            <td>{{ formatNumber(surfaceAreaCoveredInWater(planet)) }}</td>
          </tr>
        </table>
      </div>
    </template>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      error: null,
      loading: true,
      planets: null
    }
  },
  async created() {
    const response = await fetch('https://swapi.dev/api/planets/')
    this.loading = false
    if(!response.ok){
      this.error = this.errorMessage(response)
    } else {
      const data = await response.json()
      this.planets = data.results.sort((planet1, planet2) => planet1.name > planet2.name ? 1 : -1)
    }
  },
  methods: {
    errorMessage(response) {
      return `Oh no! There was an error with your response! Code: ${response.status}.`
    },
    formatNumber(number) {
      return isNaN(number) ? '?' : number.toString().replace(/(?=(\d{3})+(?!\d))/g, " ")
    },
    surfaceAreaCoveredInWater(planet) {
      const radius = planet.diameter / 2
      const totalSurfaceArea = 4 * Math.PI * radius * radius
      return parseInt(totalSurfaceArea * planet.surface_water / 100)
    }
  }
}
</script>

<style>
table {
  border-collapse: collapse;
}
th, td {
  border: 1px solid grey;
}
</style>
