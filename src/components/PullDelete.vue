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
            items: [
                0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
                0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0
            ],
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

            selectedPullItem: null,
            selectedPullButton: null,

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
        pullItemPastThreshold() {
            let pullItemLeft = this.targetPullItem.style.left.replace("px", "");
            return -pullItemLeft > this.pullThreshold / 2;
        },
        touchStart(event) {
            this.initialPos.x = event.targetTouches[0].clientX;
            this.initialPos.y = event.targetTouches[0].pageY;

            this.currentPos.x = this.initialPos.x;
            this.targetPullItem = this.getTargetPullItem(event.target);
            this.targetPullItem.button =
                this.targetPullItem.getElementsByClassName(
                    "pull-button-container"
                )[0];

            let notSelected = !this.targetPullItem.hasAttribute("selected");
            if (notSelected && this.selectedPullItem) {
                this.resetSelectedPullItem();
            }
            this.stopTargetPullItemTransition();

            this.pullItemX = this.targetPullItem.style.left.replace("px", "");
            this.diffX = this.currentPos.x - this.pullItemX;
        },
        resetSelectedPullItem() {
            this.selectedPullItem.removeAttribute("selected");
            this.animatePullItem(this.selectedPullItem, 0);
        },
        stopTargetPullItemTransition() {
            let pullItemPos = this.targetPullItem.getBoundingClientRect();
            let buttonPos = this.targetPullItem.button.getBoundingClientRect();
            let offsetRight = window.innerWidth - buttonPos.left;

            this.targetPullItem.style.left = pullItemPos.left + "px";
            this.targetPullItem.button.style.right = -offsetRight + "px";

            this.targetPullItem.style.transition = null;
            this.targetPullItem.button.style.transition = null;
        },
        touchMove(event) {
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

            if (movePosition < -this.pullThreshold) {
                movePosition = this.applyFriction(
                    movePosition,
                    this.pullThreshold
                );
            } else if (movePosition > 0) {
                movePosition = this.applyFriction(movePosition, 0);
            }
            this.setPullItemOffset(this.targetPullItem, movePosition);
        },
        applyFriction(movePos, relativePos) {
            let friction = 0.8;

            let pullDistance = movePos + relativePos;
            let pos = relativePos + Math.abs(pullDistance) ** friction;

            // tom did this
            return Math.sign(movePos) * pos;
        },
        setPullItemOffset(pullItem, offset) {
            pullItem.style.left = offset + "px";
            pullItem.button.style.right = offset + "px";
        },
        touchEnd() {
            this.autoAdjustTargetPullItem();

            if (this.pullItemPastThreshold()) {
                this.setSelectedPullItem();
            }

            this.elIsMoving = false;
            this.isScrolling = false;
            this.scrollingIsActivated = false;
        },
        setSelectedPullItem() {
            this.targetPullItem.setAttribute("selected", "");

            this.selectedPullItem = this.targetPullItem;
            this.selectedPullItem.button = this.targetPullItem.button;
        },
        autoAdjustTargetPullItem() {
            if (this.pullItemPastThreshold() && this.elIsMoving) {
                this.animatePullItem(this.targetPullItem, -this.pullThreshold);
            } else {
                this.animatePullItem(this.targetPullItem, 0);
            }
        },
        animatePullItem(pullItem, offset) {
            pullItem.style.transition = "left ease 0.5s";
            pullItem.button.style.transition = "right ease 0.5s";

            this.setPullItemOffset(pullItem, offset);
        }
    },
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
