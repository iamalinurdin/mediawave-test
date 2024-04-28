<template>
  <div class="container mx-auto p-24">
    <select class="select select-bordered w-full max-w-xs" v-model="country">
      <option value="" disabled selected>Choose country</option>
      <option v-for="(item, index) in countries" :key="index" :value="item">
        {{ item.name.common }}
      </option>
    </select>
    <div class="grid grid-cols-2 mt-5">
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
        <!-- <highchart :options="chartOptions"></highchart> -->
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      msg: 'Hello World',
      country: null,
      countries: [],
      dataCovid: null,
      latLng: [0, 0],
      chartOptions: {
        chart: {
          polar: false,
          height: "800px",
        },

        title: {
          text: "Pairs",
        },

        accessibility: {
          point: {
            valueDescriptionFormat:
              "{index}. From {point.from} to {point.to}: {point.weight}.",
          },
        },

        series: [
          {
            keys: ["from", "to", "weight"],
            data: [
              ["Brazil", "Portugal", 5],
              ["Brazil", "France", 1],
              ["Brazil", "Spain", 1],
              ["Brazil", "England", 1],
              ["Canada", "Portugal", 1],
              ["Canada", "France", 5],
              ["Canada", "England", 1],
              ["Mexico", "Portugal", 1],
              ["Mexico", "France", 1],
              ["Mexico", "Spain", 5],
              ["Mexico", "England", 1],
              ["USA", "Portugal", 1],
              ["USA", "France", 1],
              ["USA", "Spain", 1],
              ["USA", "England", 5],
              ["Portugal", "Angola", 2],
              ["Portugal", "Senegal", 1],
              ["Portugal", "Morocco", 1],
              ["Portugal", "South Africa", 3],
              ["France", "Angola", 1],
              ["France", "Senegal", 3],
              ["France", "Mali", 3],
              ["France", "Morocco", 3],
              ["France", "South Africa", 1],
              ["Spain", "Senegal", 1],
              ["Spain", "Morocco", 3],
              ["Spain", "South Africa", 1],
              ["England", "Angola", 1],
              ["England", "Senegal", 1],
              ["England", "Morocco", 2],
              ["England", "South Africa", 7],
              ["South Africa", "China", 5],
              ["South Africa", "India", 1],
              ["South Africa", "Japan", 3],
              ["Angola", "China", 5],
              ["Angola", "India", 1],
              ["Angola", "Japan", 3],
              ["Senegal", "China", 5],
              ["Senegal", "India", 1],
              ["Senegal", "Japan", 3],
              ["Mali", "China", 5],
              ["Mali", "India", 1],
              ["Mali", "Japan", 3],
              ["Morocco", "China", 5],
              ["Morocco", "India", 1],
              ["Morocco", "Japan", 3],
              ["Japan", "Brazil", 1],
            ],
            type: "dependencywheel",
            name: "Pairs",
            dataLabels: {
              color: "#333",
              textPath: {
                enabled: true,
                attributes: {
                  dy: 5,
                },
              },
              distance: 10,
            },
            size: "95%",
          },
        ],
      },
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
      const body = await response.json()
      const { cases } = body[0]

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

      this.latLng = this.country.latlng
      this.dataCovid = years
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
