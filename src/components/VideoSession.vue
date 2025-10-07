<template>
  <div class="video-container">
    <h3>Video KYC Session</h3>

    <div id="local-video" style="width: 300px; height: 200px; border: 1px solid #ccc;"></div>
    <div id="remote-video" style="width: 300px; height: 200px; border: 1px solid #ccc;"></div>

    <button @click="leaveSession">Leave</button>
  </div>
</template>

<script>
import { OpenVidu } from 'openvidu-browser';

export default {
  props: ['token'],
  data() {
    return {
      OV: null,
      session: null,
    };
  },
  mounted() {
    this.joinSession();
  },
  methods: {
    async joinSession() {
      this.OV = new OpenVidu();
      this.session = this.OV.initSession();

      // Subscribe to remote streams
      this.session.on('streamCreated', (event) => {
        this.session.subscribe(event.stream, 'remote-video');
      });

      try {
        await this.session.connect(this.token);

        // Publish local video
        const publisher = this.OV.initPublisher('local-video', {
          audioSource: undefined,
          videoSource: undefined,
          publishAudio: true,
          publishVideo: true,
          resolution: '640x480',
          frameRate: 30,
        });

        this.session.publish(publisher);
      } catch (error) {
        console.error("Error joining session:", error);
      }
    },
    leaveSession() {
      if (this.session) this.session.disconnect();
      this.$emit('left');
    }
  }
}
</script>