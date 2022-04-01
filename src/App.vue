<template>
  <div class="container">

    <div v-if="error">
      <h1>出错啦！您访问的网页不存在</h1>
    </div>

    <div class="row title">
      <div class="col-xs-12">
        <h2>Top 100 Cryptocurriencies <small>by Market Capitalization</small></h2>
      </div>
    </div>

    <div v-if="loading" class="row title">
      <div class="col-xs-12">
        <span>loading...</span>
      </div>
    </div>
    <div v-else class="row">
      <div class="col-xs-12">
        <table class="table">
          <thead>
            <tr>
              <td>#</td>
              <td>Name</td>
              <td>Market Cap</td>
              <td>Price</td>
              <td>Volume (24hs)</td>
              <td>Circuating Supply</td>
              <td>Change (24hs)</td>
              <td>Price Graph (7d)</td>
            </tr>
          </thead>
          <tbody>
            <tr v-for="currency in currencies" :key="currency.id">
              <td><span>{{ currency.rank }}</span></td>
              <td>
                <img :src="logoUrl(currency.id)" alt="currency icon">
                <strong>{{ currency.name }}</strong>
              </td>
              <td><span>${{ currency.quotes.USD.market_cap }}</span></td>
              <td><span>${{ currency.quotes.USD.price }}</span></td>
              <td><span>${{ currency.quotes.USD.volume_24h }}</span></td>
              <td><span :class="[currency.quotes.USD.percent_change_24h > 0 ? 'text-success' : 'text-danger']">{{ currency.quotes.USD.percent_change_24h }}%</span></td>
              <td>
                <img :src="graphUrl(currency.id)" alt="price graph">
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

  </div>
</template>

<script>
  export default {
    data() {
      return {
        error: true,
        loading: false,
        currencies: []
      }
    },
    methods: {
      loadCurrencies() {
        this.loading = true
        axios.get('https://api.coinmarketcap.com/v2/ticker/?start=1&limit=100&sort=rank&structure=array')
        .then(() => {
          this.currencies = response.data.data
          this.loading = false
        })
        .catch((e) => {
          this.loading = false
        })
      },
      logoUrl(currencyId) {
        return 'https://s2.coinmarketcap.com/static/img/coins/16x16/' + currencyId + '.png'
      },
      graphUrl(currencyId) {
        return 'https://s2.coinmarketcap.com/generated/sparklines/web/7d/usd/' + currencyId + '.png'
      }
    },
    filters: {
      formatNumber(value, showDecimals) {
        // we're using numeraljs to add format to the numbers
        showDecimals = showDecimals === undefined ? true: showDecimals
        var formatString = showDecimals ? ( value < 1 ? '0.000000' : '0,0.00' ) : '0.0'
        return numeral(value).format(formatString)
      }
    }
  }
</script>