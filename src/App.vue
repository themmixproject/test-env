<template>
    <terminal-window />
</template>

<script>
// import Test from "./components/TestComponent.vue";
// import PullDelete from "./components/PullDelete.vue";
import TerminalWindow from "./components/TerminalWindow.vue";

export default {
    name: "App",
    components: {
        TerminalWindow
    }
};
</script>

<style lang="scss">
body {
    margin: 0;
    padding: 0;
    background-color: black;
    color: white;
    font-family: "Courier New", Courier, monospace;
}

input {
    border: 0;
    font-family: "Courier New", Courier, monospace;
    font-size: inherit;
    padding: 0;
    outline: 0;
    background-color: black;
    color: white;
}
</style>
