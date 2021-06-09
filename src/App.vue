<template>
  <v-container>
    <v-row justify="center">
      <v-col md="6">
        <video-player :videoURL="videoURL" />
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import VideoPlayer from "./components/VideoPlayer";

export default {
  name: "App",
  components: {
    VideoPlayer,
  },
  data: () => ({
    videoURL:
      "https://archive.org/download/BigBuckBunny_124/Content/big_buck_bunny_720p_surround.mp4",
  }),
  methods: {
    changeVideoPlaying(url) {
      this.videoURL = url;
    },
  },
};
</script>

<style>
body {
  font-family: Roboto;
}
</style>
