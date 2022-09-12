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
            <div class="pull-button">Delete</div>
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
            startPosY: 0,
            elIsMoving: false,
            isScrolling: false
        };
    },
    computed: {
        movePos() {
            return this.posX - this.diffX;
        },
        passXThreshold() {
            return this.passMoveThresh(this.startPosX, this.posX);
        },
        passYThreshold() {
            return this.passMoveThresh(this.startPosY, this.posY);
        }
    },
    methods: {
        passMoveThresh(initialPos, currentPos) {
            return Math.abs(currentPos - initialPos) > 2;
        },
        touchStart(event) {
            this.posX = event.targetTouches[0].clientX;
            this.startPosX = this.posX;
            this.startPosY = event.targetTouches[0].pageY;
            this.divX = event.target.style.left.replace("px", "");
            this.diffX = this.posX - this.divX;
        },
        moveBlockToTouchPos(event) {
            let target = event.target;
            target.style.left = this.movePos + "px";
            target.children[0].style.right = this.movePos + "px";
        },
        touchMove(event) {
            if (this.elIsMoving) {
                event.preventDefault();
            }

            this.setXYPositions(event);

            if (this.passXThreshold && !this.isScrolling) {
                this.horizontalTouchMovement(event);
            } else if (this.passYThreshold && !this.elIsMoving) {
                this.isScrolling = true;
            }
        },
        setXYPositions(event) {
            this.posX = event.targetTouches[0].clientX;
            this.posY = event.targetTouches[0].pageY;
        },
        horizontalTouchMovement(event) {
            event.preventDefault();
            this.elIsMoving = true;
            this.moveBlockToTouchPos(event);
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
    background-color: rgb(198, 213, 228);
    // touch-action: none;
}

.pull-button {
    background-color: red;
    position: absolute;
    left: 100%;
    top: 0;
}
</style>
