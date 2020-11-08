<template>
  <div id="app">

    <TextArea/>

  </div>
</template>

<script>

  import TextArea from './components/TextArea'

  export default {
    name: "App",
    components: {
      TextArea
    }
  }

</script>

<style lang="scss">

  * {

    margin: 0;
    padding: 0;
    box-sizing: border-box;

  }

  body, html, #app {

    height: 100%;
    font-family: Poppins, sans-serif;

  }

  .flex-center {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  button {

    background-color: #4cb04f;
    padding: 5px 10px;
    border: 1px solid #4cb04f;
    color: white;
    border-radius: 4px;
    cursor: pointer;
    outline: none;

    &:hover {

      background-color: #fff;
      color: #4cb04f

    }

  } 

</style>
