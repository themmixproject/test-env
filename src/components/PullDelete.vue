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
            startPosX: 0,
            posY: 0,
            divY: 0,
            startPosY: 0,
            elIsMoving: false,
            isScrolling: false,
            isHorizontalTouch: false
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
        setAll(a, v) {
            var i,
                n = a.length;
            for (i = 0; i < n; ++i) {
                a[i] = v;
            }
        },
        passMoveThresh(initialPos, currentPos) {
            return Math.abs(currentPos - initialPos) > 9;
        },
        getParentPullItem(element) {
            if (element.className.match(/pull-item$/)) {
                return element;
            }
            return this.getParentPullItem(element.parentElement);
        },
        touchStart(event) {
            this.setAll(this.isTrans, false);

            this.posX = event.targetTouches[0].clientX;

            this.setStartPositions(event);

            let element = this.getParentPullItem(event.target);
            this.divX = element.style.left.replace("px", "");
            this.diffX = this.posX - this.divX;
        },
        setStartPositions(event) {
            this.startPosX = this.posX;
            this.startPosY = event.targetTouches[0].pageY;
        },
        moveBlockToTouchPos(event) {
            let element = event.target;
            if (!element.className.match(/pull-item$/)) {
                element = this.getParentPullItem(element);
            }

            element.style.left = this.movePos + "px";
            element.children[1].style.right = this.movePos + "px";
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
        touchEnd(event, index) {
            if (event.target.className.match(/pull-item/) && !this.elIsMoving) {
                this.isTrans[index] = true;
                let element = this.getParentPullItem(event.target);
                element.style.left = 0;
            }

            this.elIsMoving = false;
            this.isScrolling = false;
            this.isHorizontalTouch = false;
        }
    },
    mounted() {
        for (let i = 0; i < this.items.length; i++) {
            this.isTrans.push(false);
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
