<template>
  <div>
    <v-simple-table>
      <v-data-table
        v-model="videoSelected"
        :headers="headers"
        :items="videos"
        item-key="name"
        :custom-sort="customSort"
        class="elevation-1">
        <template v-slot:item="{ item }">
          <tr v-on:click="onOpenVideo(item.key, true)">
            <td>{{ toLocaleDate(item.lastModified) }}</td>
            <td>{{ item.key }}</td>
            <td><v-icon>star</v-icon></td>
          </tr>
        </template>
      </v-data-table>
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
    videoSelected: [],
	  videos:[],
	  dialog: false,
    src: "",
    playingIndex: 0,
    dateTime: "",
	  leftArrow: mdiChevronLeft,
    rightArrow: mdiChevronRight,
    star: mdiStar,
    headers: [
      { text: '撮影日時', value: 'datetime' },
      { text: 'ファイル名', value: 'key' },
      { text: 'お気に入り',}, 
    ],
  }),
  methods: {
    customSort: function(items, index, isDesc) {
      console.log(index)
      items.sort((a, b) => {
        if (index[0]=='datetime') {
          if (!isDesc[0]) {
              return new Date(b['lastModified']) - new Date(a['lastModified']);
          } else {
              return new Date(a['lastModified']) - new Date(b['lastModified']);
          }
        } else {
          if(typeof a[index] !== 'undefined'){
            if (!isDesc[0]) {
               return a[index].toLowerCase().localeCompare(b[index].toLowerCase());
            } else {
                return b[index].toLowerCase().localeCompare(a[index].toLowerCase());
            }
          }
        }
      })
      return items
    },  
	  padding0(num) {
	    return ('0' + num).slice(-2)
	  },
  	toLocaleDate(datestr) {
	    const d = new Date(datestr)
	    const year = d.getFullYear()
	    const month = d.getMonth()
	    const date = d.getDate()
	    const hours = d.getHours()
	    const minues = d.getMinutes()
	    const second = d.getSeconds()
	    return `${year}/${month+1}/${date} ${this.padding0(hours)}:${this.padding0(minues)}:${this.padding0(second)}`
	  },
	  async getVideos(){
      const vs = await Storage.list('')
	    this.videos.push(...vs)
    },
  	onOpenVideo(key, open){
      const index = this.videos.findIndex(element => element.key == key)  
      this.setVideoByIndex(index)
      if (open) {
        this.dialog = true
      }
    },
    setVideoByIndex(index){
      this.playingIndex = index
      this.setVideo(this.videos[index])
    },
    async setVideo(video){
      this.dateTime = this.toLocaleDate(video.lastModified)
  	  const src = await Storage.get(video.key, { expires: 120 })
      this.src = src
    },
    videoNext(){
      console.log("next")
      this.setVideoByIndex(this.playingIndex+1)
    },
    videoPrev(){
      console.log("prev")
      this.setVideoByIndex(this.playingIndex-1)
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
