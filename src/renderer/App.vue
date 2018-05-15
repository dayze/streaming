<template>
    <div id="app">
        <router-view></router-view>
    </div>
</template>

<script>
  export default {
    name: 'streaming'
  }
</script>

<style>
    #app {
        background-color: #ffffff;
        min-height: 100vh;
    }

    html {
    }
  /*  html {
        position: relative;
        min-height: 100%;
    }

    html::before {
        content: " ";
        position: absolute;
        top: 0;
        bottom: 0;
        right: 0;
        left: 0;
        background: url(assets/img/bg.jpg) top left repeat scroll;
        z-index: -1;
        filter: brightness(0.6);
        background-size: cover;
    }*/
    /*#bg-video {
        position: fixed;
        right: 0;
        bottom: 0;
        min-width: 100%;
        min-height: 100%;
        width: auto;
        height: auto;
        z-index: -100;
        background-size: cover;
        filter: brightness(0.6);
    }*/

    * {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }
</style>
