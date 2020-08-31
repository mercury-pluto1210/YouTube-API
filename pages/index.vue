<template>
  <div>
    <div class="url-search">
      <div class="row">
        <div class="col-sm-12 col-xl-12 ">
          <form action="">
            <b-input-group prepend="YouTube URL">
              <b-form-input placeholder="URL検索" v-model="urlSearch"></b-form-input>
              <b-button v-on:click="searchButton">検索</b-button>
            </b-input-group>
          </form>
        </div>
      </div>
    </div>
    <div class="youtube-contents">
      <div class="youtube-video-contents">
        <div class="youtube-video">
          <iframe v-bind:src="youtubeFrame" frameborder="0" class="video"></iframe>
        </div>
        <h2 class="title">{{ youtubeTitle }}</h2>
      </div>
      <hr class="video-comment-border">
      <div class="youtube-comment" v-for="(comment, index) in comments" :key="index">
        <p class="comment-name">{{ comment.commentName }}</p>
        <p>{{ comment.commentContent }}</p>
      </div>
      <p>{{comments.textOriginal}}</p>
    </div>
  </div>
</template>

<script>
// YouTube Data API v3 API-URL"AIzaSyDXc9SW4nCbdI3665DuI-1ozNnmtByK0NI"
import axios from 'axios'

export default {
  data(){
    return{
      youtubeFrame: '',
      urlSearch: '',
      youtubeTitle: '',
      comments: []
    }
  },
  mounted () {
  },
  methods: {
    searchButton: function(){
      if(this.urlSearch == ''){
        return
      }
      else{
        const urlHome = 'https://www.youtube.com/'
        const urlEnd = this.urlSearch.match(/.+watch\?v=(.+)/)
        this.youtubeFrame = urlHome + 'embed/' + urlEnd[1]

        const APIKEY = 'AIzaSyDXc9SW4nCbdI3665DuI-1ozNnmtByK0NI'

        const title_url = 'https://www.googleapis.com/youtube/v3/videos'
        axios
          .get(title_url + '?id=' + urlEnd[1] + '&key=' + APIKEY + '&part=snippet,contentDetails,statistics,status')
          .then(response => {
              console.log(response.data.items[0].snippet.title);
              this.youtubeTitle = response.data.items[0].snippet.title
          })
          .catch(error => {
            console.log(error)
          })

        const comment_url = 'https://www.googleapis.com/youtube/v3/commentThreads'
        axios
          .get(comment_url + '?part=snippet,replies&videoId=' + urlEnd[1] + '&key=' + APIKEY)
          .then(response => {
            for(var i = 1; i <= 5; i++){
              console.log(response.data.items[i].snippet.topLevelComment.snippet)
              this.comments.push({
                commentName: response.data.items[i].snippet.topLevelComment.snippet.authorDisplayName,
                commentContent: response.data.items[i].snippet.topLevelComment.snippet.textOriginal
              })
            }
          })
          .catch(error => {
            console.log(error)
          })
      }
    }
  }
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
}

body{
  background-color: #ffff;
  font-family: "Noto Sans Japanese", "Hiragino Kaku Gothic ProN", Meiryo, sans-serif;
}

.url-search{
  padding-top: 50px;
  width: 80%;
  margin: 0 auto;
}

.youtube-contents{
  padding: 50px;
}

.youtube-video-contents{
  padding-bottom: 30px;
}

.youtube-video{
position: relative;
  width: 100%;
  height: 0;
  padding-bottom: 56.25%;
  overflow: hidden;
  margin-bottom: 10px;
}

.video{
 width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}

.title{
  font-size: 2rem;
}

.video-comment-border{
  border-color: rgb(129, 129, 129);
  border-width: 3px;
  
}

.comment-name{
  font-weight: bolder;
}
</style>
