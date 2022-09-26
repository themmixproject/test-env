<template>
    <div id="pull-container">
        <div
            class="pull-item"
            v-for="(item, index) in items"
            :key="index"
            :class="{ trans: isTrans[index] }"
            @touchstart="touchStart($event, index)"
            @touchmove="touchMove($event, index)"
            @touchend="touchEnd($event, index)"
        >
            <div class="pull-item-content-container">
                <div class="pull-item-content">Pull to delete item</div>
            </div>
            <div class="pull-button-container">
                <div class="pull-button">
                    <div>Delete</div>
                </div>
            </div>
            <hr />
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            items: [
                0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
                0, 0
            ],
            isTrans: [],
            posX: 0,
            divX: 0,
            diffX: 0,
            movePos: 0,
            startPosX: 0,
            posY: 0,
            divY: 0,
            startPosY: 0,
            elIsMoving: false,
            isScrolling: false,
            isHorizontalTouch: false,
            selectedIndex: null,
            pullThreshold: 0,
            pullComplete: false
        };
    },
    computed: {
        passXThreshold() {
            return this.passMoveThresh(this.startPosX, this.posX);
        },
        passYThreshold() {
            return this.passMoveThresh(this.startPosY, this.posY);
        },
        pullContainerHasChildren() {
            let container = document.getElementById("pull-container");
            return this.hasChildren(container);
        }
    },
    methods: {
        setAll(a, v) {
            var i,
                n = a.length;
            for (i = 0; i < n; ++i) {
                a[i] = v;
            }
        },
        passMoveThresh(initialPos, currentPos) {
            return Math.abs(currentPos - initialPos) > 3;
        },
        getParentPullItem(element) {
            if (element.className.match(/pull-item$/)) {
                return element;
            }
            return this.getParentPullItem(element.parentElement);
        },
        touchStart(event) {
            this.posX = event.targetTouches[0].clientX;

            this.setStartPositions(event);

            let element = this.getParentPullItem(event.target);
            this.divX = element.style.left.replace("px", "");
            this.diffX = this.posX - this.divX;
        },
        hasChildren(element) {
            return element.children.length > 0;
        },
        setStartPositions(event) {
            this.startPosX = this.posX;
            this.startPosY = event.targetTouches[0].pageY;
        },
        touchMove(event) {
            if (this.elIsMoving) {
                event.preventDefault();
            }

            this.setXYPositions(event);
            let target = event.target;
            if (target.className.match(/pull-item/)) {
                this.controlPullItem(event);
            }
        },
        controlPullItem(event) {
            if (this.passXThreshold && !this.isHorizontalTouch) {
                this.isHorizontalTouch = true;
            } else if (this.passYThreshold && !this.elIsMoving) {
                this.isScrolling = true;
            }
            if (this.isHorizontalTouch && !this.isScrolling) {
                this.horizontalTouchMovement(event);
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
        moveBlockToTouchPos(event) {
            let element = event.target;
            if (!element.className.match(/pull-item$/)) {
                element = this.getParentPullItem(element);
            }

            this.movePos = this.posX - this.diffX;
            let absMovePos = Math.abs(this.movePos);

            if (this.movePos > 0) {
                this.movePos = 0;
            } else if (absMovePos > this.pullThreshold) {
                this.movePos = this.stretchPull(element);
                this.pullIsComplete = true;
            } else if (absMovePos < this.pullThreshold) {
                this.counter = 0;
            }

            element.style.left = this.movePos + "px";
            element.children[1].style.right = this.movePos + "px";

            this.oldMovePos = this.movePos;
        },
        stretchPull() {
            let friction = 0.8;
            let pullDistance = Math.abs(this.movePos) - this.pullThreshold;
            let pos = this.pullThreshold + pullDistance ** friction;

            return -pos;
        },
        touchEnd(event) {
            let element = this.getParentPullItem(event.target);
            if (
                (Math.abs(this.movePos) >= this.pullThreshold / 2 &&
                    this.elIsMoving &&
                    !this.pullIsComplete) ||
                this.pullIsComplete
            ) {
                console.log("finishPull");
                this.moveItem(element, -this.pullThreshold);
            } else if (
                (event.target.className.match(/pull-item/) &&
                    !this.elIsMoving) ||
                (Math.abs(this.movePos) < this.pullThreshold / 2 &&
                    this.elIsMoving)
            ) {
                this.resetItemPosition(element);
            }

            this.elIsMoving = false;
            this.isScrolling = false;
            this.isHorizontalTouch = false;
            this.pullIsComplete = false;
        },
        resetItemPosition(pullItem) {
            this.moveItem(pullItem, 0);
        },
        moveItem(pullItem, left) {
            // console.log("start");

            let duration = 400;

            pullItem.style.transition = "left ease " + duration / 1000 + "s";
            pullItem.children[1].style.transition =
                "right ease " + duration / 1000 + "s";
            pullItem.style.left = left + "px";
            pullItem.children[1].style.right = left + "px";
            window.setTimeout(function () {
                pullItem.style.transition = null;
                pullItem.children[1].style.transition = null;

                // console.log("end");
            }, duration);
        }
    },
    mounted() {
        let container = document.getElementById("pull-container");
        if (this.pullContainerHasChildren) {
            let button = container.getElementsByClassName(
                "pull-button-container"
            )[0];
            this.pullThreshold = button.offsetWidth;
        }
    }
};
</script>

<style lang="scss" scoped>
hr {
    margin: 0;
    border: 1px solid lightseagreen;
}

#pull-container {
    overflow: hidden;
}

.pull-item {
    text-align: center;
    font-size: 2em;
    font-family: "Gill Sans", "Gill Sans MT", Calibri, "Trebuchet MS",
        sans-serif;
    color: #4c4c4c;
    position: relative;
    background-color: rgb(198, 213, 228);
    // transition: 0.01s left ease;
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

.trans {
    transition: all 0.4s;
}
</style>
