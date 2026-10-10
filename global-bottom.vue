<template>
    <footer v-if="$slidev.nav.currentPage != 1" class="absolute bottom-0 left-0 right-0 p-2 text-sm opacity-70">
        <a href="https://asm0dey.site" target="_blank">https://asm0dey.site</a> | #dev2next | #kotlin
    </footer>
</template>

<style>
footer a { color: inherit; text-decoration: none; border-bottom: 1px dotted currentColor; }
</style>
