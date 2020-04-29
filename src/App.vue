<template>
  <v-app>
    <v-app-bar app color="primary">
      <v-icon color="white">{{ mainIcon }}</v-icon>
      <v-toolbar-title>
        <span color="white">Cat Notifier</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <amplify-sign-out></amplify-sign-out>
    </v-app-bar>

    <v-content>
      <amplify-authenticator v-bind:authConfig="authConfig"></amplify-authenticator>
      <VideoTable></VideoTable>
    </v-content>
  </v-app>
</template>

<script>
import VideoTable from './components/VideoTable.vue'
import { components } from 'aws-amplify-vue'
import { mdiBellRing } from '@mdi/js'

export default {
    name: 'App',
    components: {
	VideoTable,
	...components
    },
    data: () => ({
	mainIcon: mdiBellRing,
	authConfig: {
	    signUpConfig:{
		hideAllDefaults: true
	    }
	}
  }),
};
</script>
