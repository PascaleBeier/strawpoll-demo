<template>
  <div id="app" class="container">

    <h1>strawpoll-demo</h1>

    <div class="panel panel-default">
      <div class="panel-heading">{{ poll.name }}</div>
        <div class="panel-body">
          <form class="form-horizontal" method="post">
            <div v-for="(option, index) in poll.options" class="radio">
              <label v-if="option.name">
                <input v-show="!submitted" type="radio" name="id" :value="index" v-model="voted">
                {{ option.name }} <span v-show="results">- {{ option.votes }} Votes</span>
              </label>
            </div>
            <hr>
              <a @click="vote()" class="btn btn-primary" :disabled="submitted">Vote!</a>
              <a @click="results=!results" class="pull-right btn btn-default">Toggle Results</a>
          </form>
        </div>
      </div>

  </div>
</template>

<script>
import firebase from 'firebase'
import config from './../firebase-config'

firebase.initializeApp(config)

const db = firebase.database()
const ref = db.ref('poll')

export default {
  data () {
    return {
      poll: {},
      voted: '',
      submitted: false,
      results: false
    }
  },
  created () {
    const self = this
    ref.on('value', polls => self.poll = polls.val())
  },
  methods: {
    vote () {
      if (!this.submitted && this.voted >= 0) {
        let votesRef = db.ref('poll/options/' + this.voted + '/votes')
        votesRef.transaction(votes => votes +1)
        this.submitted = true
        this.results = true
      }
    }
  }
}
</script>

<style>
@import url("https://cdn.jsdelivr.net/bootstrap/3.3.6/css/bootstrap.css");
</style>
