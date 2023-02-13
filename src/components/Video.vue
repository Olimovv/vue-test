<template>
    <div>
        <video ref="video" autoPlay/>
    </div>
</template>

<script>
import io from 'socket.io-client';

export default {
    name: 'Stream',
    data() {
        return {
            socket: null,
            stream: null,
            interval: null,
            socketURL: {
                type: String,
                default: 'ws://localhost:3000'
            },
            ioOptions: {
                type: Object,  // NOTE: use these options: https://socket.io/docs/v4/client-options/
                default() {
                    return { rejectUnauthorized: false, transports: ['websocket'] };
                }
            },
        };
    },
    mounted() {

        navigator.mediaDevices
            .getUserMedia({
                video: true
            })
            .then(stream => {
                this.stream = stream;
                this.$refs.video.srcObject = stream;
                this.socket = io('ws://localhost:3000', this.ioOptions);
                console.log('socket',this.socket)
                this.socket.emit('stream', stream);
            });
    },
    beforeDestroy() {
        clearInterval(this.interval);
        this.socket.close();
        this.stream.getTracks().forEach(track => track.stop());
    }
};
</script>