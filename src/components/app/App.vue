<template>
    <Video></Video>
</template>
<script>
import Video from "../Video.vue";

export default {
    components: {
        Video,
    },
}
</script>
<style>
.search-panel {
    margin-top: 2rem;
    padding: 1.5rem;
    background-color: #fcfaf5;
    border-radius: 4px;
    box-shadow: 15px 15px 15px rgba(0, 0, 0, 0.15);
}

.app {
    height: 100vh;
    color: #000;
}

.content {
    width: 1000px;
    min-height: 700px;
    background-color: #fff;
    margin: 0 auto;
    padding: 5rem 0;
}
</style>

