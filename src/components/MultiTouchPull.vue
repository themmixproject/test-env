<template>
    <div id="container">
        <div
            id="slider"
            @mousedown="mouseDown($event)"
            @mousemove="mouseMove($event)"
        ></div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            diffX: 0,
            elIsMoving: false,
            isScrolling: false,
            initialPos: {
                x: 0,
                y: 0
            },
            currentPos: {
                x: 0,
                y: 0
            },
            // pullItemX: 0,
            isMouseDown: false,

            slider: null,

            sliderX: 0,

            // targetPullItem: null,
            // targetPullButton: null,

            // selectedPullItem: null,
            // selectedPullButton: null,

            pullThreshold: 0
        };
    },
    computed: {
        passXThreshold() {
            return this.passMoveThresh(this.initialPos.x, this.currentPos.x);
        },
        passYThreshold() {
            return this.passMoveThresh(this.initialPos.y, this.currentPos.y);
        }
    },
    methods: {
        passMoveThresh(initialPos, currentPos) {
            return Math.abs(currentPos - initialPos) > 2;
        },
        mouseDown(event) {
            this.initialPos.x = event.clientX;
            this.currentPos.x = this.initialPos.x;
            this.isMouseDown = true;
            this.sliderX = this.slider.style.left.replace("px", "");

            this.diffX = this.currentPos.x - this.sliderX;
            console.log(this.diffX);
        },
        mouseMove(event) {
            this.currentPos.x = event.clientX;
            if (this.passXThreshold && this.isMouseDown) {
                this.horizontalMoveEvent(event);
            }
        },
        horizontalMoveEvent(event) {
            event.preventDefault();

            this.elIsMoving = true;

            let movePosition = this.currentPos.x - this.diffX;

            console.log(movePosition);
            this.slider.style.left = movePosition + "px";
        }
    },
    mounted() {
        this.slider = document.getElementById("slider");
    }
};
</script>
<style lang="scss" scoped>
#container {
    position: relative;
    height: 200px;

    #slider {
        height: 50px;
        width: 300px;
        background-color: grey;
        position: absolute;
    }
}
</style>
style
