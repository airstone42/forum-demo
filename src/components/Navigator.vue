<template>
  <div class="navigator">
    <div class="nav navbar navbar-expand-md navbar-dark fixed-top bg-dark" role="navigation">
      <router-link to="/" class="navbar-brand">Forum Demo</router-link>
      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse" aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarCollapse">
        <form @submit.prevent="submit" class="form-inline my-2 my-md-0">
          <input v-model="search" class="form-control" type="text" placeholder="Search" aria-label="Search">
        </form>
        <ul v-if="!session || !session.user_id" class="navbar-nav ml-auto">
          <li class="nav-item mr-auto">
            <router-link to="/register" class="nav-link">Register</router-link>
          </li>
          <li class="nav-item mr-auto">
            <router-link to="/login" class="nav-link">Login</router-link>
          </li>
        </ul>
        <ul v-else class="navbar-nav ml-auto">
          <li class="nav-item dropdown">
            <a class="nav-link dropdown-toggle" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">Welcome, {{session.user_name}}! </a>
            <div class="dropdown-menu" aria-labelledby="dropdown">
              <router-link :to="'/user/' + session.user_id" class="dropdown-item">Update information</router-link>
              <router-link to="/favorites" class="dropdown-item">View favorites </router-link>
              <router-link to="/message" class="dropdown-item">View messages</router-link>
              <router-link to="/create" v-if="session.user_is_admin" class="dropdown-item">Create new board</router-link>
            </div>
          </li>
          <li class="nav-item">
            <router-link to="/logout" class="nav-link">Logout</router-link>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'navigator',
    props: {
      session: {
        user_id: null,
        user_is_admin: null,
        user_name: null
      }
    },
    data() {
      return {
        search: null
      }
    },
    methods: {
      submit() {
        const search = this.search
        this.$router.push({
          name: 'search',
          params: { search },
          props: true
        })
      }
    }
  }
</script>
