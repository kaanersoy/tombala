<template>
  <div class="page-description">
    <div class="col">
      <img src="../assets/tombala-logo.png" alt="Tombala Logo">
    </div>
    <div class="col">
      <p>
        <span>tombala</span> is a Giveaway machine.<br>
        It allows you to create giveaways using <span>Youtube</span> comments.
      </p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Description'
}
</script>

<style>
.page-description{
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 50px 0;
}
.page-description .col:last-child{
  margin-left: 20px;
}
.page-description .col img{
  display: block;
  margin: 0 auto;
  font-size: 150px;
  width: 1em;
  height: 1em;
}
.page-description .col p{
  font-size: 18px;
}
.page-description .col p span{
  color: rgb(var(--main-color));
  font-weight: bold;
  font-size: 22px;
}
</style>