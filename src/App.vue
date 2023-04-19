<template>
  <div id="app">
    <Sidebar/>
    <router-view/>
  </div>
</template>



<script>
import Sidebar from "./components/Sidebar.vue";

export default {
  name:'App',
  components:{
    Sidebar,

  }
}
</script>

<style>
@import '//at.alicdn.com/t/c/font_3960754_v0pkazbxwq.css';
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
html,body,#app{
  height: 100vh;
}
body{
  font-family: 'Avenir', Helvetica,Arial,sans-serif ;
  font-size: 14px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: #eee;
}
/*.btn{*/
/*  color: #666;*/
/*  font-size: 12px;*/
/*  padding: 2px 4px;*/
/*  background-color: #ffffff;*/
/*  box-shadow: 0px 0px 2px 0px #ccc;*/
/*  border: none;*/
/*  cursor: pointer;*/
/*  display: inline-block;*/
/*}*/


#app{
  display: flex;
  align-items: stretch;
}
</style>
