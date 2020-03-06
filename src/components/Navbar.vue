<template>
<div id="navbar">

<nav class="uk-navbar-container uk-margin" uk-navbar>
    <div class="uk-navbar-left">

        <a class="uk-navbar-item uk-logo" :href="profileLink" target="_blank" style="color: #00cc99;">{{ username }}</a>

        <div class="uk-navbar-item uk-navbar-right" style="width: 40%;">
            <input class="uk-input uk-form-width-small" type="text" placeholder="Recherche..." style="width: 100%;" v-model="searching">
        </div>

    </div>
</nav>

</div>
</template>

<script>
export default {
  name: 'Navbar',
  data () {
    return {
      searching: ''
    }
  },
  props: {
    username: String,
    profileLink: String
  },
  watch: {
    searching: function (val) {
      if (val !== '') {
        this.$parent.searchItem(val)
      } else {
        this.$parent.research = { }
      }
    }
  }
}
</script>

<style scoped>
</style>
