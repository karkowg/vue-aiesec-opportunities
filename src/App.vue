<template lang="html">
  <div id="app">
    <div class="header">
      <h3>vue-aiesec-opportunities (gt)</h3>
      <span>opportunities: {{ opportunities.length }}</span>
    </div>
    <div class="filters">
      <h4>static filters applied:</h4>
      <h4><a href="#" @click="toggleBackgrounds">--backgrounds ({{ showBackgrounds ? '-' : '+'}})</a></h4>
      <div class="filter" v-if="showBackgrounds">
        <span class="filter-item" v-for="background in filteredBackgrounds(apiBackgrounds)">{{ background.text }};</span>
      </div>
      <h4><a href="#" @click="toggleMcs">--mcs ({{ showMcs ? '-' : '+'}})</a></h4>
      <div class="filter" v-if="showMcs">
        <span class="filter-item" v-for="mc in filteredMcs(apiMcs)">{{ mc.text }};</span>
      </div>
    </div>
    <div v-if="!hasData" class="loading">Loading ...</div>
    <ul class="opportunities">
      <li class="opportunity-item" v-for="opp in opportunities" :key="opp.id" :style="{ order: flexDescOrder(opp.id) }">
        <opportunity :opportunity="opp"></opportunity>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'
import backgrounds from './config/backgrounds.js'
import filters from './config/filters.js'
import mcs from './config/mcs.js'
import Opportunity from './components/Opportunity'

export default {
  name: 'app',
  components: {
    Opportunity
  },
  data () {
    return {
      opportunities: [],
      baseUrl: '//gis-api.aiesec.org/v2/opportunities.json?only=data&per_page=250',
      apiBackgrounds: backgrounds.all,
      apiMcs: mcs.all,
      showBackgrounds: false,
      showMcs: false
    }
  },
  computed: {
    token () {
      return `&access_token=${filters.token}`
    },
    mcs () {
      let query = ''
      filters.mcs.forEach(mc => {
        query += `&filters[home_mcs][]=${mc}`
      })
      return query
    },
    backgrounds () {
      let query = ''
      filters.backgrounds.forEach(background => {
        query += `&filters[backgrounds][][id]=${background}`
      })
      return query
    },
    programmes () {
      let query = ''
      filters.programmes.forEach(prog => {
        query += `&filters[programmes][]=${prog}`
      })
      return query
    },
    opportunitiesUrl () {
      return this.baseUrl + this.token + this.mcs + this.backgrounds + this.programmes
    },
    hasData () {
      return !!this.opportunities.length
    }
  },
  methods: {
    fetchOpportunities () {
      let vm = this
      axios.get(this.opportunitiesUrl).then(response => {
        response.status === 200 && (vm.opportunities = response.data.data)
      })
    },
    flexDescOrder (id) {
      return 9999999 - id
    },
    filteredBackgrounds (backgrounds) {
      return backgrounds.filter(background => {
        return filters.backgrounds.indexOf(background.id) > -1
      })
    },
    filteredMcs (mcs) {
      return mcs.filter(mc => filters.mcs.indexOf(mc.id) > -1)
    },
    toggleBackgrounds () {
      this.showBackgrounds = !this.showBackgrounds
    },
    toggleMcs () {
      this.showMcs = !this.showMcs
    }
  },
  created () {
    this.fetchOpportunities()
  }
}
</script>

<style lang="scss">
* {
  box-sizing: border-box;
  list-style: none;
  margin: 0;
  padding: 0;
  text-decoration: none;
  vertical-align: baseline;
}

a {
  color: #888888;
}

body {
  background: linear-gradient(60deg, #2f4f4f, #284343);
}

#app {
  color: #2c3e50;
  font-family: monospace;
  min-height: 98vh;
  padding: 16px 0;

  .header {
    color: #fff;
    display: inline-flex;
    justify-content: space-around;
    padding: 8px 16px 0;
    width: 100%;
  }

  .loading {
    align-items: center;
    color: #fff;
    display: flex;
    font-size: 2em;
    height: 70vh;
    justify-content: center;
  }

  .filters {
    color: #fff;
    padding: 16px 16px 0;

    .filter {
      display: inline-block;
      word-break: break-all;
    }

    .filter-item {
      margin-left: 8px;
    }
  }

  .opportunities {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
    padding: 16px 16px 0;
    width: 100%;

    .opportunity-item {
      align-items: center;
      background: #d5eaea;
      border-radius: 4px;
      box-shadow: 3px -3px 1px 0px #888888;
      display: flex;
      flex: 0 1 260px;
      height: 172px;
      margin-right: 6px;
      margin-top: 12px;
      overflow: hidden;
      padding: 8px;
      white-space: nowrap;
    }
  }
}
</style>
