<template>
  <div>
    <v-simple-table>
      <thead>
      <tr>
        <th class="text-left">撮影日時</th>
        <th class="text-left">ファイル名</th>
        <th>お気に入り</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(item, index) in videos" :key="item.name" v-on:click="onOpenVideo(index, true)">
        <td>{{ toLocaleDate(item.lastModified) }}</td>
        <td>{{ item.key }}</td>
        <td><v-icon color="grey">{{star}}</v-icon></td>  
      </tr>
    </tbody>
    </v-simple-table>
    <v-dialog v-model="dialog">
      <div class="dialog-wrapper">
	      <div class="arrow left" v-on:click="videoPrev">
  	      <v-icon color="grey darken-2" size="70px">{{leftArrow}}</v-icon>
	      </div>
        <div class="video-wrapper">
          <p class="title">撮影日時 : <time>{{dateTime}}</time></p>
  	      <video :src="src" autoplay class="video"></video>
        </div>
	      <div class="arrow right" v-on:click="videoNext">
  	      <v-icon color="grey darken-2" size="70px">{{rightArrow}}</v-icon>
        </div>
      </div>
    </v-dialog>
  </div>
</template>

<script>
import Storage from '@aws-amplify/storage';
import { mdiChevronRight, mdiChevronLeft, mdiStar } from '@mdi/js'

export default{
  name: 'VideoTable',
  data: () => ({
	  videos:[],
	  dialog: false,
    src: "",
    playingIndex: 0,
    dateTime: "",
	  leftArrow: mdiChevronLeft,
    rightArrow: mdiChevronRight,
    star: mdiStar,
  }),
  methods: {
	  padding0(num) {
	    return ('0' + num).slice(-2)
	  },
  	toLocaleDate(datestr) {
	    console.log(datestr)
	    const d = new Date(datestr)
	    console.log(d)
	    const year = d.getFullYear()
	    const month = d.getMonth()
	    const date = d.getDate()
	    const hours = d.getHours()
	    const minues = d.getMinutes()
	    const second = d.getSeconds()
	    return `${year}/${month+1}/${date} ${this.padding0(hours)}:${this.padding0(minues)}:${this.padding0(second)}`
	  },
	  async getVideos(){
	    const videos = await Storage.list('')
	    this.videos.push(...videos)
	  },
  	async onOpenVideo(index, open){
      this.playingIndex = index
      const video = this.videos[index]
	    const src = await Storage.get(video.key, { expires: 120 })
      this.src = src
      this.dateTime = this.toLocaleDate(video.lastModified)
      if (open) {
        this.dialog = true
      }
	  },
    videoNext(){
      console.log("next")
      this.onOpenVideo(this.playingIndex+1, false)
    },
    videoPrev(){
      console.log("prev")
      this.onOpenVideo(this.playingIndex-1, false)
    }
  },
  created(){
        this.getVideos()
  }
}
</script>

<style lang="scss" scoped>
.dialog-wrapper{
  display:flex;
  justify-content: center;
  .arrow {
    position: absolute;
    bottom: 50%;
    z-index: 2;
    &.left{
      left: 0;
    }
    &.right{
      right: 0;
    }
  }
  .video-wrapper { 
    .title{
      background-color: gray;
    }
  }
}
</style>
