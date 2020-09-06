<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<style lang="scss">
*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: inherit; 
}

html {
  font-size: 62.5%; 

  @media (max-width: 75em) {
    font-size: 56.25%;  
  }

  @media (max-width: 56.25em) {
    font-size: 50%;  
  }

  @media (min-width: 112.5em) {
    font-size: 75%;  
  }

}


body {
  box-sizing: border-box;
}

#app{
  background-image: linear-gradient(to right bottom, #ffb900, #ff7730); 
  background-repeat: no-repeat;
  width: 100%;
  height: 100vh;
}


.swal2-container{
  z-index: 9999999999999999999999999 !important;
}
</style>
