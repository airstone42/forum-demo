<template>
  <div class="board-component">
    <div class="threads" v-for="thread in threads" :key="thread.thread_id">
      <div v-if="thread.thread_id">
        <div class="container">
          <div class="row">
            <div class="col">
              <h2>{{thread.thread_title}}</h2>
              <p class="float-right">
                <router-link :to="'/user/' + thread.user_id">{{thread.user_name}}</router-link>
                {{thread.thread_time}}
              </p>
            </div>
          </div>
          <div class="row">
            <div class="col">
              <button v-on:click="deleteThread(thread.thread_id)" v-if="session.user_is_admin" class="btn btn-danger ">&times; Delete</button>
              <p class="float-right">
                <button v-if="thread.favorite && session.user_id" v-on:click="favorite(thread.thread_id)" class="btn btn-success">Favorited</button>
                <button v-if="!thread.favorite && session.user_id" v-on:click="favorite(thread.thread_id)" class="btn btn-light">Favorite</button>
                <router-link :to="'/read/' + thread.thread_id" class="btn btn-secondary" role="button">View &raquo;</router-link>
              </p>
            </div>
          </div>
          <div v-if="!thread.thread_visible" class="tip row">
            <div v-if="tip.status === 'success'" class="alert alert-success">{{tip.message}}</div>
            <div v-if="tip.status === 'fail'" class="alert alert-danger">{{tip.message}}</div>
          </div>
          <hr>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'board-component',
    props: {
      session: {
        user_id: null,
        user_is_admin: null,
        user_name: null
      },
      info: {
        board_id: null,
        board_name: null,
        board_intro: null
      },
      threads: {
        thread: {
          thread_id: null,
          thread_title: null,
          thread_visible: null,
          user_name: null,
          user_id: null,
          thread_time: null,
          favorite: null,
        }
      }
    },
    data() {
      return {
        tip: {
          status: null,
          message: null,
        }
      }
    },
    methods: {
      deleteThread(thread_id) {
        const self = this
        this.threads.forEach((item) => {
          if (item.thread_id === thread_id) {
            if (!item.thread_visible)
              item.thread_visible = true
            else if (item.thread_visible)
              item.thread_visible = false
          }
        })
        fetch(api + '/delete', this.post({ thread_id: thread_id }))
          .then((response) => {
            return response.json()
          })
          .then((data) => {
            if (data.toString() === '1') {
              self.tip.status = 'success'
              self.tip.message = 'Delete successfully!'
              setTimeout(() => {
                self.tip.message = 'Reloading in 2 seconds.'
              }, 1000)
              setTimeout(() => {
                self.tip.status = null
                self.tip.message = null
                self.$emit('update')
                self.$emit('reload')
              }, 2000)
            } else if (data.toString() === '0') {
              self.tip.status = 'fail'
              self.tip.message = 'Delete failed!!'
              setTimeout(() => {
                self.tip.status = null
                self.tip.message = null .
                self.$emit('reload')
              }, 2000)
            }
          })
      },
      favorite(thread_id) {
        const self = this
        fetch(api + '/favorite', this.post({ thread_id: thread_id }))
          .then((response) => {
            return response.json()
          })
          .then(() => {
            self.$emit('reload')
          })
      }
    }
  }
</script>

<style scoped>
  .alert {
    width: 100%;
    padding: 15px;
    margin: auto;
  }

  .threads:first-child {
    padding-top: 30px;
  }

  .threads:last-child {
    padding-bottom: 30px;
  }

  .threads .tip {
    padding-top: 30px;
    padding-bottom: 30px;
  }

  .threads .btn {
    margin-left: 5px;
    margin-right: 5px;
  }
</style>
