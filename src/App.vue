<template>
  <div>
    <Header></Header>
    <div class="container">
      <Description></Description>
      <div class="form-wrapper" v-if="!isGiveawaySuccesfull">
        <form @submit.prevent="getComments(getVideoID())" autocomplete="off">
          <div class="form-group">
            <label>Youtube Video URL</label>
            <input v-model="videoUrl" @change="getVideoID" type="url" required="required">
          </div>
          <div class="video-info" v-if="isUrlAvailable">
            <div class="box">
              <img :src="videoInfo.thumbnails.default.url" width="150" height="120" :alt="videoInfo.title">
            </div>
            <div class="box">
              <h4>
                {{videoInfo.title}}
              </h4>
              <p>{{videoInfo.description.substring(0, 140)}}...</p>
              <small><span>Video by:</span> {{videoInfo.channelTitle}}</small>
            </div>
          </div>
          <div class="form-group">
            <label>Size of <span>Giveaway</span></label>
            <input type="number" v-model="giveAwaySize" required="required">
          </div>
          <div class="checkbox-group">
            <input type="checkbox" v-model="isKeywordBasedSearchActive" id="keyword-checkbox" name="keyword-checkbox">
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
      <GiveawayWinnersList></GiveawayWinnersList>
    </div>
  </div>
</template>

<script>
import GiveawayWinnersList from './components/GiveawayWinnersList';
import Header from './components/Header';
import Description from './components/Description';
import axios from 'axios';
export default {
  name: 'App',
  components: {
    Header,
    Description,
    GiveawayWinnersList
  },
  data: function(){
    return{
      isKeywordBasedSearchActive: false,
      comments: null,
      videoInfo: null,
      videoUrl: null,
      isUrlAvailable: null,
      giveAwaySize: 0,
      isGiveawaySuccesfull: false,
      uniqueUserComments: null,
      giveawayWinners: [],
    }
  },
  methods:{
    getVideoInfo: async function(videoID){
      const apiKey = process.env.VUE_APP_YOUTUBE_API;
      try{
        await axios
          .get(`https://youtube.googleapis.com/youtube/v3/videos?part=snippet&contentDetails&statistics&id=${videoID}&key=${apiKey}`)
          .then(res => {
            this.videoInfo = res.data.items[0].snippet;
          })
          this.isUrlAvailable = true;
      }catch(err){
        this.isUrlAvailable = false;
      }
    },
    getComments: async function(videoId){
      const apiKey = process.env.VUE_APP_YOUTUBE_API;
      axios
        .get(`https://www.googleapis.com/youtube/v3/commentThreads?key=${apiKey}&textFormat=plainText&part=snippet&videoId=${videoId}&maxResults=100`)
        .then(res => {
          this.comments = res.data.items;
          this.createGiveaway(this.comments);
        })
    },
    getVideoID: function(){
      const urlString = this.videoUrl;
      try{
        const url = new URL(urlString);
        const videoID = url.searchParams.get('v');
        this.getVideoInfo(videoID);
        return videoID;
      }catch{
        this.isUrlAvailable = false;
      }
    },
    createGiveaway: function(comments){
      let uniqueUserComments = []
      comments.forEach(el => {
        const userId = el.snippet.topLevelComment.snippet.authorChannelId;
        if(!uniqueUserComments.includes(userId)) 
          uniqueUserComments.push(el);
      });
      let giveawayWinners = []
      for (let i = 0; i < this.giveAwaySize; i++) {
        const randomIndex = parseInt(Math.random() * uniqueUserComments.length);
        giveawayWinners.push(uniqueUserComments[randomIndex]);
      }
      this.giveawayWinners = giveawayWinners;
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
  /* #aaaaaa */
  --contrasted-text: 170, 170, 170;
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
.container .form-wrapper form .video-info{
  display: flex; 
  align-items: center;
  margin-bottom: 10px;
}
.container .form-wrapper form .video-info .box{}
.container .form-wrapper form .video-info .box:last-child{
  margin-left: 20px;
}
.container .form-wrapper form .video-info .box img{}
.container .form-wrapper form .video-info .box h4{
  margin-bottom: 8px;
  color: rgb(var(--main-color));
  font-size: 18px;
}
.container .form-wrapper form .video-info .box p{
  margin-bottom: 8px;
  font-size: 15px;
}
.container .form-wrapper form .video-info .box small{
  display: block;
  font-size: 13px;
  color: rgb(var(--contrasted-text))
}
.container .form-wrapper form .video-info .box small span{
  color: rgb(var(--main-color));
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
  color: rgb(var(--contrasted-text));
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
.container .form-wrapper form .button-container{}
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
