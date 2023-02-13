<template>
    <div>
        <video ref="localVideo" id="localVideo" autoplay muted></video>
        <button @click="startStreaming">Start Streaming</button>
    </div>
</template>

<script>
import axios from "axios";

export default {
    data() {
        return {
            localStream: null,
            peerConnection: null,
            dataChannel: null,
        };
    },
    methods: {
        startStreaming() {
            navigator.mediaDevices
                .getUserMedia({ video: true, audio: true })
                .then((stream) => {
                    this.localStream = stream;
                    this.$refs.localVideo.srcObject = stream;

                    this.peerConnection = new RTCPeerConnection();
                    this.peerConnection.addStream(stream);

                    this.dataChannel = this.peerConnection.createDataChannel("myDataChannel");
                    this.dataChannel.onopen = this.handleDataChannelOpen;
                    this.dataChannel.onmessage = this.handleDataChannelMessage;

                    this.peerConnection
                        .createOffer()
                        .then((offer) => this.peerConnection.setLocalDescription(offer))
                        .then(() => {
                            // send the offer to a server to be forwarded to the other peer
                            axios.post("/offer", {
                                offer: this.peerConnection.localDescription,
                            });
                        });

                    this.peerConnection.onicecandidate = (event) => {
                        if (event.candidate) {
                            // send the candidate to a server to be forwarded to the other peer
                            axios.post("/candidate", {
                                candidate: event.candidate,
                            });
                        }
                    };
                })
                .catch((error) => {
                    console.error(error);
                });
        },
        handleDataChannelOpen(event) {
            console.log("Data channel is open and ready to be used.", event);
        },
        handleDataChannelMessage(event) {
            console.log("Data channel message received.", event.data);
        },
    },
};
</script>
