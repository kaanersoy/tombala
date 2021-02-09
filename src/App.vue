<template>
  <div id="app">
    <Preloader v-if="isPreloaderActive"></Preloader>
    <Header></Header>
    <div class="container">
      <Description></Description>
      <div class="error-label" v-if="errorMessage">
        <p>{{errorMessage}}</p>
      </div>
      <div class="form-wrapper" v-show="!isGiveawaySuccesfull">
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
            <input type="number" v-model="giveAwaySize" min="1" max="20" required="required">
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
            <input type="text" v-model="keyword">
          </div>
          <div class="button-container">
            <button>tombala!</button>
          </div>
        </form>
      </div>
      <div class="go-back-button" v-if="isGiveawaySuccesfull">
        <button @click="giveAwayToggler">
          <div class="box">
            <svg version="1.1" id="Capa_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
              width="459px" height="459px" viewBox="0 0 459 459" style="enable-background:new 0 0 459 459;" xml:space="preserve">
              <g>
                <g id="reply">
                  <path d="M178.5,140.25v-102L0,216.75l178.5,178.5V290.7c127.5,0,216.75,40.8,280.5,130.05C433.5,293.25,357,165.75,178.5,140.25z"
                    />
                </g>
              </g>
            </svg>
          </div>
          <div class="box">
            Go back
          </div>
        </button>
      </div>
      <GiveawayWinnersList v-if="isGiveawaySuccesfull" :winners="giveawayWinners"></GiveawayWinnersList>
    </div>
    <Footer></Footer>
  </div>
</template>

<script>
import GiveawayWinnersList from './components/GiveawayWinnersList';
import Header from './components/Header';
import Description from './components/Description';
import Preloader from './components/Preloader';
import Footer from './components/Footer';
import axios from 'axios';
export default {
  name: 'App',
  components: {
    Header,
    Description,
    GiveawayWinnersList,
    Preloader,
    Footer
  },
  data: function(){
    return{
      isKeywordBasedSearchActive: false,
      keyword: null,
      comments: null,
      videoInfo: null,
      videoUrl: null,
      isUrlAvailable: null,
      giveAwaySize: 1,
      isGiveawaySuccesfull: false,
      uniqueUserComments: null,
      giveawayWinners: [],
      isPreloaderActive: false,
      errorMessage: null
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
        console.log(err);
        this.isUrlAvailable = false;
      }
    },
    getComments: async function(videoId){
      if(this.videoUrl == null || this.videoUrl == ""){
        this.errorMessage = "URL can't be empty";
        return false;
      }
      if(videoId == false){
        this.errorMessage = "Please enter a valid URL!";
        return false; 
      }else{
        this.errorMessage = null;
        const apiKey = process.env.VUE_APP_YOUTUBE_API;
        try{
          this.isPreloaderActive = true;
          axios
            .get(`https://www.googleapis.com/youtube/v3/commentThreads?key=${apiKey}&textFormat=plainText&part=snippet&videoId=${videoId}&maxResults=100`)
            .then(res => {
              this.comments = res.data.items;
              this.createGiveaway(this.comments);
              this.isPreloaderActive = false;
            })
        }catch(err){
          console.log(err);
        }
      }
    },
    getVideoID: function(){
      const urlString = this.videoUrl;
      try{
        let url = new URL(urlString);
        if(!url.searchParams.get('v')){
          return false;
        }else{
          this.errorMessage = null;
          const videoID = url.searchParams.get('v');
          this.getVideoInfo(videoID);
          return videoID;
        }
      }catch{
        if(this.videoUrl == null || this.videoUrl == ""){
          this.errorMessage = "URL can't be empty";
          return false;
        }
      }
    },
    createGiveaway: function(comments){
      let uniqueUserComments = [];
      comments.forEach(el => {
        const userId = el.snippet.topLevelComment.snippet.authorChannelId;
        if(!uniqueUserComments.includes(userId)) 
          uniqueUserComments.push(el);
      });
      let giveawayWinners = [];
      let tryCounter = 0;
      while (giveawayWinners.length < this.giveAwaySize) {
        const randomIndex = parseInt(Math.random() * uniqueUserComments.length);
        if(!giveawayWinners.includes(uniqueUserComments[randomIndex])){
          if(this.isKeywordBasedSearchActive){
            const usersComment = uniqueUserComments[randomIndex].snippet.topLevelComment.snippet.textDisplay.toLowerCase();
            if(usersComment.includes(this.keyword.toLowerCase())){
              giveawayWinners.push(uniqueUserComments[randomIndex]);
            }
            tryCounter++;
          }
          else{
            giveawayWinners.push(uniqueUserComments[randomIndex]);
          }
        }
        if(tryCounter > this.comments.length) break; 
      }
      
      this.giveawayWinners = giveawayWinners;
      this.giveAwayToggler();
    },
    giveAwayToggler: function(){
    this.isGiveawaySuccesfull = !this.isGiveawaySuccesfull;
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap');
:root{
  /* #e7406c */
  --main-color: 231, 64, 108;
  /* #edf0f1 */
  --body-bg: 237, 240, 241;
  /* #aaaaaa */
  --contrasted-text: 170, 170, 170;
}
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
img{
  vertical-align: middle;
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
  margin-bottom: 50px;
}
@media only screen and (max-width: 1024px) {
  .container{
    width: 80%;
  }
  .container .form-wrapper form .checkbox-group label{
    flex-shrink: 0;
  }
}
.container .error-label{
  width: 100%;
  padding: 20px;
  text-align: center;
  background-color: #f3acac;
  margin: 15px 0;
  border-radius: 15px;
  font-size: 15px;
}
.container .error-label p{}
.container .go-back-button{
  margin: 15px 0;
}
.container .go-back-button button{
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  border-radius: 20px;
  border: 5px solid #f3acac;
  padding: 10px 0;
  background-color: rgba(var(--main-color), 0.8);
  color: #fff;
}
.container .go-back-button button .box{}
.container .go-back-button button .box:last-child{margin-left: 15px;}
.container .go-back-button button .box svg{ fill: #fff; width: 25px;height: 25px;}
.container .form-wrapper form{
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
@media only screen and (max-width: 414px) {
  .container .form-wrapper form .video-info{
    align-items: flex-start;
    margin: 20px 0;
  }
  .container .form-wrapper form .video-info .box img{
    width: 65px;
    height: 50px;
  }
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
  font-size: 18px;
  padding: 15px 18px;
  border-radius: 10px;
  background-color: rgb(var(--main-color));
  display: block;
  margin-left: auto;
}
</style>
