<template>
  <div class="home">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    {{ msg }}
  </div>
</template>

<script>
export default {
  name: "Home",
  data: function() {
    return {
      msg: "welcome to my component lib"
    };
  },
  methods: {}
};
</script>
