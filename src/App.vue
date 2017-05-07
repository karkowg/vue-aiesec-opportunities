<template lang="html">
  <div id="app">
    <div class="header">
      <h3>vue-aiesec-opportunities</h3>
    </div>
    <div class="filters">
      <div class="counter">
        <span>--filters (<a id="apply-filters" @click="fetchOpportunities">apply</a>)</span>
        <span>total: {{ opportunities.length }}</span>
      </div>
      <div class="selects">
        <multiselect v-model="filters.bgValues" :options="bgOptions" :limit="1" :multiple="true" :close-on-select="false" :max-height="200" placeholder="backgrounds" label="text" track-by="id"></multiselect>
        <multiselect v-model="filters.mcValues" :options="mcOptions" :limit="1" :multiple="true" :close-on-select="false" :max-height="200" placeholder="mcs" label="text" track-by="id"></multiselect>
        <multiselect v-model="filters.prValues" :options="prOptions" :limit="1" :multiple="true" :close-on-select="false" :max-height="200" placeholder="programmes" label="consumer_name" track-by="id"></multiselect>
      </div>
    </div>
    <div class="content">
      <div v-show="!hasData" class="loading">
        {{ loading ? 'loading ...' : '~ no opportunities found ~' }}
      </div>
      <ul class="opportunities">
        <li class="opportunity-item" v-for="opp in opportunities" :key="opp.id" :style="{ order: flexDescOrder(opp.id) }">
          <opportunity :opportunity="opp"></opportunity>
        </li>
      </ul>
    </div>
    <div class="footer">
      <span>by: { dev: <a href="//karkowg.github.io" target="_blank">karkowg</a> }</span>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import backgrounds from './config/backgrounds.js'
import mcs from './config/mcs.js'
import programmes from './config/programmes.js'
import Multiselect from 'vue-multiselect'
import Opportunity from './components/Opportunity'

export default {
  name: 'app',
  components: {
    Multiselect,
    Opportunity
  },
  data () {
    return {
      auth: {
        token: 'e316ebe109dd84ed16734e5161a2d236d0a7e6daf499941f7c110078e3c75493'
      },
      baseUrl: '//gis-api.aiesec.org/v2/opportunities.json?only=data&per_page=300',
      filters: {
        bgValues: [],
        mcValues: [],
        prValues: []
      },
      bgOptions: backgrounds.all,
      mcOptions: mcs.all,
      prOptions: programmes.all,
      loading: false,
      opportunities: []
    }
  },
  computed: {
    token () {
      return `&access_token=${this.auth.token}`
    },
    mcs () {
      let query = ''
      this.filters.mcValues.forEach(mc => {
        query += `&filters[home_mcs][]=${mc.id}`
      })
      return query
    },
    backgrounds () {
      let query = ''
      this.filters.bgValues.forEach(background => {
        query += `&filters[backgrounds][][id]=${background.id}`
      })
      return query
    },
    programmes () {
      let query = ''
      this.filters.prValues.forEach(prog => {
        query += `&filters[programmes][]=${prog.id}`
      })
      return query
    },
    opportunitiesUrl () {
      return this.baseUrl + this.token + this.mcs + this.backgrounds + this.programmes
    },
    hasData () {
      return !!this.opportunities.length
    },
    hasFilter () {
      return (this.filters.bgValues.length + this.filters.mcValues.length + this.filters.prValues.length) > 1
    }
  },
  methods: {
    fetchOpportunities () {
      let vm = this
      if (vm.hasFilter) {
        vm.setOpportunities([])
        vm.setLoading(true)

        let prom = axios.get(this.opportunitiesUrl)

        setTimeout(() => {
          prom.then(response => {
            if (response.status === 200) {
              vm.setLoading(false)
              !!response.data.data.length && vm.setOpportunities(response.data.data)
            }
          })
        }, 800)
      } else {
        alert('Apply at least two filters, please (:')
      }
    },
    setOpportunities (opportunities) {
      this.opportunities = opportunities
    },
    setLoading (state) {
      this.loading = state
    },
    flexDescOrder (id) {
      return 9999999 - id
    }
  }
}
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>

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
  padding: 8px 0 16px;

  .header {
    color: #fff;
    display: flex;
    justify-content: center;
    padding: 8px 16px 0;
  }

  .filters {
    color: #fff;
    padding: 16px 16px 0;

    #apply-filters {
      cursor: pointer;
    }

    .counter {
      display: flex;
      justify-content: space-between;
    }

    .selects {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;

      .multiselect {
        margin-top: 4px;
      }

      @media screen and (min-width: 480px) {
        .multiselect {
          width: 33%;
        }
      }
    }
  }

  .content {
    display: flex;
    flex-direction: column;
    padding: 16px 16px 0;
    justify-content: center;
    min-height: 75vh;

    .loading {
      align-items: center;
      color: #fff;
      display: flex;
      font-size: 2em;
      justify-content: center;
    }

    .opportunities {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-around;
      width: 100%;

      .opportunity-item {
        align-items: center;
        background: #d5eaea;
        border-radius: 4px;
        box-shadow: 3px -3px 1px 0px #888888;
        display: flex;
        flex: 0 1 280px;
        height: 172px;
        margin-right: 6px;
        margin-top: 12px;
        overflow: hidden;
        padding: 8px;
        white-space: nowrap;
      }
    }
  }

  .footer {
    color: #fff;
    display: flex;
    justify-content: center;
    padding: 16px 16px 0;
  }
}
</style>
