<template>
    <div id="pull-container">
        <div
            v-for="(item, index) in items"
            :key="index"
            class="pull-item"
            @touchstart="touchStart($event, index)"
            @touchmove="touchMove($event, index)"
        >
            Pull to delete item
            <div class="pull-button"></div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            items: [0, 0, 0],
            posX: 0,
            divX: 0,
            diffX: 0,
            distanceMoved: 0
        };
    },
    methods: {
        touchStart(event) {
            this.posX = event.targetTouches[0].clientX;
            this.divX = event.target.style.left.replace("px", "");
            this.diffX = this.posX - this.divX;
        },
        touchMove(event) {
            this.posX = event.targetTouches[0].clientX;
            let aX = this.posX - this.diffX;

            let target = event.target;
            target.style.left = aX + "px";
        }
    }
};
</script>

<style lang="scss" scoped>
#pull-container {
    overflow: hidden;
}

.pull-item {
    text-align: center;
    font-size: 5em;
    font-family: "Gill Sans", "Gill Sans MT", Calibri, "Trebuchet MS",
        sans-serif;
    border-color: #b8bcba;
    border-style: solid;
    border-width: 1px 0;
    padding-left: 0.5rem;
    color: #4c4c4c;
    box-sizing: border-box;
    position: relative;
}
</style>
