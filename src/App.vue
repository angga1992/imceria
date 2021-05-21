<template>
  <div id="app">
    <h1>Picsum Photos</h1>
    <!-- <pre>{{data}}</pre> -->
    <div class="user" v-for="(items, idx) in data" :key="idx">
      <div class="user-avatar">
        <img :src="items.download_url" />
      </div>
      <div class="user-details">
        <h2 class="user-name">
          {{ items.author }}
        </h2>
        <span>source:</span>
        <a :href="items.url">{{items.url}}</a>
      </div>
    </div>
    <button v-show="!mobile" @click="loadMore()">load more</button>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      data: [],
      page: 1,
      mobile: false
    }
  },
  methods: {
    getInitialUsers () {
      axios.get(`https://picsum.photos/v2/list?page=${this.page}&limit=10`).then((response) => {
        this.data = response.data
      })
    },
    getNextUser () {
      window.onscroll = () => {
        const bottomOfWindow = document.documentElement.scrollTop + window.innerHeight === document.documentElement.offsetHeight
        if (window.innerWidth > 600) {
          this.mobile = false
        } else {
          this.mobile = true
        }
        if (bottomOfWindow) {
          axios.get(`https://picsum.photos/v2/list?page=${this.page}&limit=10`).then(response => {
            for (const i in response.data) {
              this.data.push(response.data[i])
            }
            this.page = this.page + 1
          })
        }
      }
    },
    loadMore () {
      axios.get(`https://picsum.photos/v2/list?page=${this.page}&limit=10`).then(response => {
        for (const i in response.data) {
          this.data.push(response.data[i])
        }
        this.page = this.page + 1
      })
    }
  },
  beforeMount () {
    this.getInitialUsers()
  },
  mounted () {
    this.getNextUser()
    alert('change to mobile break point to active infinite scroll function')
  }
}
</script>

<style lang="scss" scoped>
#app {
    padding: 10%;
    background-color: antiquewhite;
    h1 {
        color: green;
    }
    .user {
        display: flex;
        background:white;
        border-radius: 0.65em;
        margin: 1em auto;
        -webkit-box-shadow: 0 10px 6px -6px #777;
        -moz-box-shadow: 0 10px 6px -6px #777;
        box-shadow: 0 10px 6px -6px #777;
        // border: 0.5px solid #777;
        .user-avatar {
            padding: 1em;
            img {
                display: block;
                width: 64px;
                /* min-width: 64px; */
                height: 64px;
                border-radius: 50%;
            }
        }
        .user-details {
            padding: 1em;
            span {
                padding-right: 4px;
                font-size: 12px;
            }
            a {
                font-size: 12px;
                word-break: break-word;
            }
            .user-name {
                margin: 0;
                padding: 0;
                font-size: 2vw;
                font-weight: 900;
            }
        }
    }
}
</style>
