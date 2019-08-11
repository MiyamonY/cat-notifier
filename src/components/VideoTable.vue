<template>
   <v-simple-table>
    <thead>
      <tr>
        <th class="text-left">日時</th>
        <th class="text-left">ファイル名</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in videos" :key="item.name">
        <td>{{ item.datetime }}</td>
        <td>{{ item.name }}</td>
      </tr>
    </tbody>
  </v-simple-table>
</template>

<script>
  import Storage from '@aws-amplify/storage';

export default{
    name: 'VideoTable',
    data: () => ({
	videos:[]
    }),
    methods: {
	async getVideos(){
	    const videos = await Storage.list('videos/');
	    this.videos.push(...videos)
	}
    },
    created(){
	this.getVideos()
    }
}
</script>
