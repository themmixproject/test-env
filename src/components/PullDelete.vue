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
            <div class="pull-content">Pull to delete item</div>
            <div class="pull-button-container">
                <div class="pull-button">Delete</div>
            </div>
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
            initialPos: {
                x: 0,
                y: 0
            },
            currentPos: {
                x: 0,
                y: 0
            },
            pullItemX: 0,

            targetPullItem: null,
            targetPullButton: null,

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
        getTargetPullItem(element) {
            if (element.className.match(/pull-item$/)) {
                return element;
            }
            return this.getTargetPullItem(element.parentElement);
        },
        passMoveThresh(initialPos, currentPos) {
            return Math.abs(currentPos - initialPos) > 2;
        },
        touchStart(event) {
            this.initialPos.x = event.targetTouches[0].clientX;
            this.initialPos.y = event.targetTouches[0].pageY;

            this.currentPos.x = this.initialPos.x;
            this.targetPullItem = this.getTargetPullItem(event.target);
            this.targetPullButton = this.targetPullItem.getElementsByClassName(
                "pull-button-container"
            )[0];

            this.pullItemX = this.targetPullItem.style.left.replace("px", "");
            this.diffX = this.currentPos.x - this.pullItemX;
        },
        touchMove(event) {
            if (this.elIsMoving) {
                event.preventDefault();
            }

            this.currentPos.x = event.targetTouches[0].clientX;
            this.currentPos.y = event.targetTouches[0].pageY;

            if (this.passXThreshold && !this.isScrolling) {
                this.horizontalTouchEvent(event);
            } else if (this.passYThreshold && !this.elIsMoving) {
                this.isScrolling = true;
            }
        },
        horizontalTouchEvent(event) {
            event.preventDefault();
            this.elIsMoving = true;

            let movePosition = this.currentPos.x - this.diffX;
            this.targetPullItem.style.left = movePosition + "px";
            this.targetPullButton.style.right = movePosition + "px";
        },
        touchEnd() {
            this.elIsMoving = false;
            this.isScrolling = false;
            this.scrollingIsActivated = false;
    mounted() {
        let pullContainer = document.getElementById("pull-container");
        let pullContainerHasChildren = pullContainer.children.length > 0;

        if (pullContainerHasChildren) {
            let button = pullContainer.getElementsByClassName(
                "pull-button-container"
            )[0];
            this.pullThreshold = button.offsetWidth;
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

.pull-button {
    display: flex;
    flex-direction: column;
    justify-content: center;
    height: 100%;
    padding: 0 10px;
}

.pull-button-container {
    background-color: red;
    position: absolute;
    left: 100%;
    top: 0;
    bottom: 0;
}
</style>
