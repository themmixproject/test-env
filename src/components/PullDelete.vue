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
            diffX: 0,
            elIsMoving: false,
            isScrolling: false,

            currentX: 0,
            initialX: 0,
            initialY: 0,
            pullItemX: 0
        };
    },
    computed: {
        movePos() {
            return this.currentX - this.diffX;
        },
        passXThreshold() {
            return this.passMoveThresh(this.initialX, this.currentX);
        },
        passYThreshold() {
            return this.passMoveThresh(this.initialY, this.currentY);
        }
    },
    methods: {
        passMoveThresh(initialPos, currentPos) {
            return Math.abs(currentPos - initialPos) > 2;
        },
        touchStart(event) {
            this.initialX = event.targetTouches[0].clientX;
            this.currentX = this.initialX;

            this.initialY = event.targetTouches[0].pageY;
            this.pullItemX = event.target.style.left.replace("px", "");
            this.diffX = this.currentX - this.pullItemX;
        },
        touchMove(event) {
            if (this.elIsMoving) {
                event.preventDefault();
            }

            this.currentX = event.targetTouches[0].clientX;
            this.currentY = event.targetTouches[0].pageY;

            if (this.passXThreshold && !this.isScrolling) {
                this.horizontalTouchMovement(event);
            } else if (this.passYThreshold && !this.elIsMoving) {
                this.isScrolling = true;
            }
        },
        horizontalTouchMovement(event) {
            event.preventDefault();
            this.elIsMoving = true;
            this.moveBlockToTouchPos(event);
        },
        moveBlockToTouchPos(event) {
            console.log(this.movePos);

            let target = event.target;
            target.style.left = this.movePos + "px";
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
