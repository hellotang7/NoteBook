<template>
  <span :title="username">{{ slug }}</span>
</template>

<script>
import {mapGetters, mapActions} from "vuex";

export default {
  data() {
    return{}
  },

  created() {
   this.setUser()
  },

  methods:{
    ...mapActions(
      {'setUser': 'checkLogin'}
    )
  },


  computed: {
    ...mapGetters([
      'username',
      'slug'
    ]),
    slug() {
      return this.username[0]
    }
  }
}
</script>

<style scoped>
span {

  width: 40px;
  height: 40px;

  border-radius: 50%;
  background: #f2b81c;
  color: #ffffff;
  font-size: 26px;

  margin-top: 15px;
  margin-bottom: 18px;
  text-shadow: 1px 0 1px #795c19;

  display: inline-block;
  text-align: center;
  line-height: 40px;
}
</style>
