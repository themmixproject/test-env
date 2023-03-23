<template>
    <terminal-line
        ><slot>Question :</slot>
        <form @submit="submitThis($event)">
            <input type="text" id="prompt-input" />
        </form>
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
    methods: {
        submitThis(event) {
            event.preventDefault();
            console.log("hello world!");
            document.getElementById("prompt-input").style.color = "red";
        }
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
