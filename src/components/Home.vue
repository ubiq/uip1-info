<template>
  <div>
    <b-row class="justify-content-md-center">
      <b-col md="8">
        <h1>{{ msg }}</h1>
        <p>{{ desc1 }}</p>
        <p>{{ desc2 }}</p>
        <b-row style="margin-bottom: 15px;">
          <b-col md="4">
            <b-card>
              <strong slot="header">Latest Block</strong>
              <h3>{{ latestBlock }}</h3>
            </b-card>
          </b-col>
          <b-col md="4">
            <b-card>
              <strong slot="header">Fork Block</strong>
              <h3>{{ forkBlock }}</h3>
            </b-card>
          </b-col>
          <b-col md="4">
            <b-card>
              <strong slot="header">Blocks Remaining</strong>
              <h3>{{ blocksRemaining }}</h3>
            </b-card>
          </b-col>
        </b-row>
        <h5>Estimated Fork Time: {{ estimatedTime }}</h5>
        <h2 style="margin-top:40px;">Essential Links</h2>
        <ul>
          <li>
            <a
              href="https://ubiqsmart.com/escher/#/proposal/0xfcd0b547b89f001c17d50df98cf10ce406da5518"
              target="_blank"
            >
              Proposal
            </a>
          </li>
          <li>
            <a
              href="https://blog.ubiqsmart.com/introducing-ubqhash-8fa515befd7"
              target="_blank"
            >
              Introducing Ubqhash
            </a>
          </li>
          <li>
            <a
              href="https://blog.ubiqsmart.com/announcing-the-uip-1-hard-fork-84d9b8b3b924"
              target="_blank"
            >
              Announcing the UIP 1 Hard Fork
            </a>
          </li>
        </ul>
        <h2>Ecosystem</h2>
        <ul>
          <li>
            <a
              href="https://github.com/ubiq/go-ubiq/releases/tag/v2.0.1"
              target="_blank"
            >
              go-ubiq
            </a>
          </li>
          <li>
            <a
              href="https://github.com/ubiq/ubqminer/releases/tag/v0.17.0-alpha.1.ubqhash"
              target="_blank"
            >
              ubqminer
            </a>
          </li>
          <li>
            <a
              href="https://github.com/ubiq/open-ethereum-pool"
              target="_blank"
            >
              open-ethereum-pool
            </a>
          </li>
          <li>
            <a
              href="https://github.com/ubiq/ubqhash"
              target="_blank"
            >
              ubqhash
            </a>
          </li>
        </ul>
      </b-col>
    </b-row>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Home',
  data () {
    return {
      msg: 'Ubiq Improvement Proposal (UIP) #1',
      desc1: 'At the end of May 2018, UIP 1 was presented to the community, discussed and voted upon. The community gave clear direction to the development team, with 56.4% of all the Escher supply taking part and a resounding 95.7% of participants voting to design and implement a new Proof-of-Work algorithm. Once the results came in, work began immediately on coding the new algorithm (Ubqhash).',
      desc2: 'The hard fork changing to Ubqhash will occur during a DAG/Epoch switch, which takes place every 30,000 blocks (approximately every 30.5 days). Ubiq is scheduled to hard fork at block 660,000. This corresponds to mining Epoch 22.',
      latestBlock: 0,
      forkBlock: 660000,
      blocksRemaining: 1,
      estimatedTime: '',
      errors: []
    }
  },
  created () {
    this.fetch()
    let self = this
    setInterval(function () {
      self.fetch()
    }, 60000) // 1 minute
  },
  methods: {
    fetch: function () {
      axios.get('https://v3.ubiqscan.io/status')
        .then(response => {
          if (response.data) {
            this.latestBlock = response.data.latestBlock.number
            if (this.latestBlock < this.forkBlock) {
              this.blocksRemaining = this.forkBlock - this.latestBlock
            } else {
              this.blocksRemaining = 0
            }
            let seconds = this.blocksRemaining * 88
            let latestTimestamp = response.data.latestBlock.timestamp
            let timestamp = (latestTimestamp + seconds) * 1000
            this.estimatedTime = this.$moment.utc(timestamp).format('lll') + ' (UTC)'
          }
        })
        .catch(e => {
          this.errors.push(e)
        })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
