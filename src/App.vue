<template>
    <div style="overflow: hidden">
        <Test />
    </div>
</template>

<script>
import Test from "./components/TestComponent.vue";

export default {
    name: "App",
    components: {
        Test
    }
};
</script>

<style lang="scss">
body {
    font-family: "Quicksand";
}
h1 {
    text-align: center;
    margin: 30px auto;
    font-size: 18px;
}
.row {
    background-color: #fafafa;
}

html {
    font-size: 625%; /*100 ÷ 16 × 100% = 625%*/
    height: 100%;
}
@media screen and (min-width: 360px) and (max-width: 374px) and (orientation: portrait) {
    html {
        font-size: 703%;
    }
}
@media screen and (min-width: 375px) and (max-width: 383px) and (orientation: portrait) {
    html {
        font-size: 732.4%;
    }
}
@media screen and (min-width: 384px) and (max-width: 399px) and (orientation: portrait) {
    html {
        font-size: 750%;
    }
}
@media screen and (min-width: 400px) and (max-width: 413px) and (orientation: portrait) {
    html {
        font-size: 781.25%;
    }
}
@media screen and (min-width: 414px) and (max-width: 431px) and (orientation: portrait) {
    html {
        font-size: 808.6%;
    }
}
@media screen and (min-width: 432px) and (max-width: 479px) and (orientation: portrait) {
    html {
        font-size: 843.75%;
    }
}

#app {
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}

body {
    background: #eeeeee;
    position: relative;
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
    overflow-x: hidden;
    overflow-y: hidden;
}

.row {
    height: 1.5rem;
    line-height: 1.5rem;
    background: #fff;
    border-color: #b8bcba;
    border-style: solid;
    border-width: 1px 0;
    font-size: 0.4rem;
    margin-top: 0.2rem;
    padding-left: 0.5rem;
    color: #4c4c4c;
    box-sizing: border-box;
}

.pull_delete {
    position: relative;
    width: calc(100% + 2.5rem) !important;
}

.pull_delete.trans {
    transition: all 0.4s;
}

.pull_delete .pd_btn {
    display: block !important;
    position: absolute !important;
    right: 0 !important;
    top: 0 !important;
    bottom: 0 !important;
    width: 2.5rem !important;
    font-size: 0.4rem !important;
    background: #ff411c !important;
    color: #ffffff !important;
    text-align: center !important;
}

.pull_delete .pd_btn::before {
    content: "DELETE";
    font-style: normal !important;
    font-size: 0.4rem !important;
}
</style>
