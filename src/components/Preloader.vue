<template>
  <div class="preloader">
    <div class="svg-container">
      <svg width="100" height="100" viewBox="0 0 100 100" fill="none" xmlns="http://www.w3.org/2000/svg">
        <circle cx="50" cy="50" r="45" stroke="#D6D6D6" stroke-width="6"/>
        <!-- <path id="circle" d="M95 50C95 57.7399 93.0037 65.3489 89.2039 72.0919C85.4042 78.8349 79.9295 84.4838 73.3088 88.4928L50 50H95Z" stroke="#E7406C" stroke-width="6"/> -->
        <circle id="circle" cx="50" cy="50" r="45" stroke="#D6D6D6" stroke-width="6"/>
      </svg>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Preloader'
}
</script>

<style scoped>
  .preloader{
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: rgba(255, 255, 255, 0.5);
    z-index: 2;
  }
  .svg-container{
    width: 50px;
    height: 50px;
    transform: translate(-50%,-50%);
    background-color: rgba(255, 255, 255, 0.);
  }
  .svg-container svg{
    width: 100%;
    height: 100%;
  }
  #circle{
    stroke-dasharray: 124;
    stroke: rgb(var(--main-color));
    animation: infiniteLoader 1s infinite linear;
    
  }
  @keyframes infiniteLoader {
    0%{
      stroke-dashoffset: 0;
    }
    100%{
      stroke-dashoffset: 255;
    }
  }
</style>