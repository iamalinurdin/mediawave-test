<template>
  <div class="container mx-auto p-24">
    <h1 class="text-center text-3xl">Covid App</h1>
    <select class="select select-bordered w-full max-w-xs" v-model="country">
      <option value="" disabled selected>Choose country</option>
      <option v-for="(item, index) in countries" :key="index" :value="item">
        {{ item.name.common }}
      </option>
    </select>
    <div class="grid grid-cols-2 gap-10 mt-5 h-[500px]">
      <div class="col-span-1">
        <div class="w-full h-[500px]">
          <client-only>
            <l-map :zoom=1 :center="[0, 0]">
              <l-tile-layer url="http://{s}.tile.osm.org/{z}/{x}/{y}.png"></l-tile-layer>
              <l-marker :lat-lng="latLng"></l-marker>
            </l-map>
          </client-only>
        </div>
      </div>
      <div class="col-span-1">
        <Tutorial :series="dataCovid" :categories="['2020', '2021', '2022', '2023', '2024']" v-if="dataCovid" />
      </div>
    </div>
  </div>
</template>

<script>
import Tutorial from '../components/Tutorial.vue'

export default {
  name: 'IndexPage',
  data() {
    return {
      msg: 'Hello World',
      country: null,
      countries: [],
      dataCovid: null,
      latLng: [0, 0],
    }
  },
  methods: {
    async fetchCountries() {
      const response = await fetch('https://restcountries.com/v3.1/all')
      const body = await response.json()

      this.countries = body
    },
    async fetchDataCovid() {
      const response = await fetch(`https://api.api-ninjas.com/v1/covid19?country=${this.country.name.common}`, {
        headers: {
          'X-Api-Key': 'bWdPiCDxjsZecggGSNK8Sg==4vJC7dNPwDOSngD4'
        }
      })
      const years = {
        2020: {
          total: 0,
          new: 0,
        },
        2021: {
          total: 0,
          new: 0,
        },
        2022: {
          total: 0,
          new: 0,
        },
        2023: {
          total: 0,
          new: 0,
        },
        2024: {
          total: 0,
          new: 0,
        },
      }
      // const years = ['2020', '2021', '2022', '2023', '2024']
      const body = await response.json()
      const { cases } = body[0]
      const series = {
        latest: [],
        total: [],
      }

      // console.log(cases)

      // const { cases } = await dataCovid.value[0]

      for (const item in cases) {
        for (const year in years) {
          if (item.includes(year)) {
            years[year].total += cases[item].total
            years[year].new += cases[item].new
          }
        }
      }

      for (const item in years) {
        series.latest.push(years[item].new)
        series.total.push(years[item].total)
      }

      this.latLng = this.country.latlng
      this.dataCovid = series
    }
  },
  watch: {
    country: {
      async handler (newValue, oldValue) {
        // console.log(newValue, oldValue)
        await this.fetchDataCovid()
      }
    }
  },
  async mounted() {
    await this.fetchCountries()
  }
}
</script>
