<template>
  <div>
    <Header></Header>
    <div class="container">
      <Description></Description>
      <div class="form-wrapper">
        <form @submit.prevent="apiConnection" autocomplete="off">
          <div class="form-group">
            <label>Youtube Video URL</label>
            <input type="text">
          </div>
          <div class="video-display">
          </div>
          <div class="form-group">
            <label>Size of <span>Giveaway</span></label>
            <input type="number">
          </div>
          <div class="checkbox-group">
            <input type="checkbox" v-model="isKeywordBasedSearchActive"  id="keyword-checkbox" name="keyword-checkbox">
            <label for="keyword-checkbox">
              <div class="slider"></div>
            </label>
            <span>Keyword based Giveaway <small>(optional)</small></span>
          </div>
          <div class="form-group" v-if="isKeywordBasedSearchActive">
            <label>Enter your <span>keyword</span> <small>(example: IMHERE)</small></label>
            <input type="text">
          </div>
          <div class="button-container">
            <button>Giveaway Time!</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import Header from './components/Header';
import Description from './components/Description';
import axios from 'axios';
export default {
  name: 'App',
  components: {
    Header,
    Description
  },
  data: function(){
    return{
      isKeywordBasedSearchActive: false,
      comments: null
    }
  },
  methods:{
    apiConnection: async function(){
      const apiKey = process.env.VUE_APP_YOUTUBE_API;
      axios
      .get(`https://www.googleapis.com/youtube/v3/commentThreads?key=${apiKey}&textFormat=plainText&part=snippet&videoId=F-DNIiYyeio&maxResults=100`)
      .then(res => {
        console.log(res);
        this.comments = res.data.items;
      })
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap');
:root{
  /* #e7406c */
  --main-color: 231, 64, 108;
  --body-bg: 237, 240, 241;
}
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
*::before, *::after{
  box-sizing: inherit;
}
body {
  font-family: 'Roboto', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: rgb(var(--body-bg));
  font-size: 19px;
}
.container{
  width: 800px;
  margin: 0 auto;
}
.container .form-wrapper form{
  padding: 15px;
  display: block;
}
.container .form-wrapper form .form-group{
  margin-bottom: 15px;
}
.container .form-wrapper form .form-group:nth-last-child(1){

}
.container .form-wrapper form .form-group label{
  margin-bottom: 5px;
  display: block;
}
.container .form-wrapper form .form-group label span{
  color: rgb(var(--main-color));
}
.container .form-wrapper form .form-group label small{
  color: #aaaaaa;
}
.container .form-wrapper form .form-group input{ 
  width: 100%;
  border: 1px solid rgb(var(--main-color));
  padding: 8px 4px;
  display: block;
  border-radius: 5px;
  color: rgb(var(--main-color));
  font-size: 25px;
  transition: 100ms ease-in;
}
.container .form-wrapper form .form-group input:focus{
  outline: rgb(var(--main-color));
  box-shadow: 0px 0px 5px rgba(var(--main-color), 0.3);
}
.container .form-wrapper form .checkbox-group{
  display: flex;
  align-items: center;
  margin-bottom: 15px;
}
.container .form-wrapper form .checkbox-group span{
  margin-left: 10px;
}
.container .form-wrapper form .checkbox-group span small{
  color: rgb(var(--main-color));
}

.container .form-wrapper form .checkbox-group label{
  width: 60px;
  height: 34px;
  display: block;
  position: relative;
  border: 2px solid rgb(var(--main-color));
  border-radius: 80px;
}
.container .form-wrapper form .checkbox-group label .slider{
  position: absolute;
  top: 2px; 
  left: 4px;
  width: 26px;
  height: 26px;
  background-color: rgb(var(--main-color));
  border-radius: 50%;
  transition: 150ms ease-in-out;
}
.container .form-wrapper form .checkbox-group input{
  visibility: hidden;
  display: none;
}

.container .form-wrapper form .checkbox-group input:checked ~ label{
  background-color: rgb(var(--main-color));
}
.container .form-wrapper form .checkbox-group input:checked ~ label .slider{
  transform: translateX(22px);
  background-color: rgb(var(--body-bg));
}
.container .form-wrapper form .button-container {}
.container .form-wrapper form .button-container button{
  border: none;
  box-shadow: 0 0 50px rgb(200, 200, 200);
  color: #fff;
  padding: 12px 0;
  border-radius: 10px;
  background-color: rgb(var(--main-color));
  display: block;
  width: 18%;
  margin-left: auto;
}
</style>
