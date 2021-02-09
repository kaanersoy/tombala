<template>
  <header>
    <div class="logo">
      <img src="../assets/tombala-logo.png" />
    </div>
    <div>
      <h1>tombala</h1>
    </div>
  </header>
</template>

<script>
export default {

}
</script>

<style scoped>
    header{
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;
      padding: 15px;
      background-color: rgba(var(--main-color),0.2);
      text-align: center;
    }
    header img{
      width: 48px;
      height: 48px;
    }
    header h1{
      font-weight: 300;
      letter-spacing: 5px;
      font-size: 16px;
    }
    @media only screen and (max-width: 414px) {
      header{
        width: 100%;
      }
    }
</style>