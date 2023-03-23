<template>
    <terminal-line
        ><slot>Question :</slot>
        <input type="text" v-model="promptValue" />
    </terminal-line>
</template>

<script>
import TerminalLine from "./TerminalLine.vue";

export default {
    data() {
        return {
            promptValue: ""
        };
    },
    components: {
        TerminalLine
    }
};
</script>

<style lang="scss" scoped>
input {
    display: block;
    width: 100%;
    resize: none;
    outline: none;
    border: none;
    box-sizing: border-box;
}
</style>
