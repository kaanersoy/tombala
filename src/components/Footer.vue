<template>
  <footer>
    <div class="content-wrapper">
      <p>
        Created by <a href="https://github.com/kaanersoy" target="_blank">Kaan Ersoy</a>
      </p>
      <p>
        Source Code : <a href="https://github.com/kaanersoy/tombala" target="_blank">github/tombala</a>
      </p>
    </div>
  </footer>
</template>

<script>
export default {
  name: 'Footer'
}
</script>

<style scoped>
footer{
  font-size: 15px;
  width: 100%;
  padding: 30px 0;
}
footer div{
  text-align: center;
}
footer div p {
  color: rgb(var(--contrasted-text));
}
footer div p a{
  color: rgb(var(--main-color));
}

</style>