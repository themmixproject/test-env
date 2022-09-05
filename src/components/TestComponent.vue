<template>
    <div
        class="row pull_delete"
        v-for="(item, index) in items"
        :key="index"
        :class="{ trans: isTrans[index] }"
        @touchstart="touchTest($event)"
        @touchmove="moveBar($event, index)"
        @touchend="endMove($event, index)"
    >
        Sliding to the left can be deleted.
        <button class="pd_btn"></button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            items: [0, 0, 0, 0, 0],
            isTrans: [false, false, false, false, false],

            startX: 0,
            startY: 0,
            pullX: 0,
            pullY: 0,
            moveX: 0,
            moveY: 0,

            rem: 0,
            btn: null,
            threshold: 0
        };
    },
    methods: {
        touchTest(event) {
            this.startX = event.targetTouches[0].pageX;
            this.startY = event.targetTouches[0].pageY;

            this.setAll(this.isTrans, false);
        },
        setAll(a, v) {
            var i,
                n = a.length;
            for (i = 0; i < n; ++i) {
                a[i] = v;
            }
        },
        moveBar(event) {
            this.pullX = event.targetTouches[0].pageX;
            this.pullY = event.targetTouches[0].pageY;
            this.moveX = (this.startX - this.pullX) * 0.8;
            this.moveY = (this.startY - this.pullY) * 0.8;

            let target = event.target;
            if (Math.abs(this.moveY) > Math.abs(this.moveX)) return;

            if (Math.abs(this.moveX) <= Math.abs(this.btn)) {
                target.style.transform = "translateX(-" + this.moveX + "px)";
            }
        },
        endMove(event, index) {
            let target = event.target;

            let trans = this.transVerseShift(target, index);
            let viewTrans =
                trans >= this.threshold && trans < this.btn ? this.btn : 0;
            this.isTrans[index] = true;
            target.style.transform = "translateX(-" + viewTrans + "px)";

            if (target.className == "pd_btn") {
                event.preventDefault();
            }
        },
        transVerseShift(target) {
            let computedStyle = window.getComputedStyle(target);
            let trans = computedStyle["transform"];

            if (trans.indexOf("matrix") > -1) {
                return Math.abs(trans.split(",")[4]) || 0;
            } else {
                return Math.abs(trans.replace(/[^\d.]/g, "")) || 0;
            }
        },
        indexInClass(node) {
            let className = node.className;
            let myClass = document.getElementsByClassName(className);

            let num = 0;
            for (let i = 0; i < myClass.length; i++) {
                if (myClass[i] === node) {
                    return num;
                }
                num++;
            }
            return -1;
        }
    },
    mounted() {
        let computedStyle = window.getComputedStyle(document.documentElement);
        this.rem = parseFloat(computedStyle.fontSize.replace("px", ""));
        this.btn = 2.5 * this.rem;
        this.threshold = this.btn / 2;
    }
};
</script>

<style lang="scss"></style>
