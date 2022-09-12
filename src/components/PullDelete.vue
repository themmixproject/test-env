<template>
    <div id="pull-container">
        <div
            v-for="(item, index) in items"
            :key="index"
            class="pull-item"
            @touchstart="touchStart($event, index)"
            @touchmove="touchMove($event, index)"
            @touchend="touchEnd($event, index)"
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
            items: [0, 0, 0, 0, 0],
            posX: 0,
            divX: 0,
            diffX: 0,
            startPosX: 0,
            posY: 0,
            divY: 0,
            diffY: 0,
            startPosY: 0,
            distanceMoved: 0,
            elIsMoving: false,
            isScrolling: false,
            scrollingIsActivated: false
        };
    },
    methods: {
        touchStart(event) {
            this.posX = event.targetTouches[0].clientX;
            // this.startPosX = event.targetTouches[0].clientX
            this.startPosX = this.posX;
            this.startPosY = event.targetTouches[0].pageY;
            this.divX = event.target.style.left.replace("px", "");
            this.diffX = this.posX - this.divX;
        },
        touchMove(event) {
            if (this.elIsMoving) {
                event.preventDefault();
            }

            this.posX = event.targetTouches[0].clientX;
            let aX = this.posX - this.diffX;

            let posY = event.targetTouches[0].pageY;

            if (Math.abs(this.posX - this.startPosX) > 3 && !this.isScrolling) {
                event.preventDefault();
                this.elIsMoving = true;
                let target = event.target;
                target.style.left = aX + "px";
            } else if (
                Math.abs(posY - this.startPosY) > 3 &&
                !this.scrollingIsActivated &&
                !this.elIsMoving
            ) {
                this.isScrolling = true;
                this.scrollingIsActivated = true;
            }
        },
        touchEnd() {
            this.elIsMoving = false;
            this.isScrolling = false;
            this.scrollingIsActivated = false;
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
    // touch-action: none;
}
</style>
