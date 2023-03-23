<template>
    <div class="terminal-line">
        <div class="terminal-line-prefix">$~:>&nbsp;</div>
        <div class="terminal-line-content">
            <slot></slot>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.terminal-line {
    display: flex;
}

.terminal-line-content {
    width: 100%;
}
</style>
