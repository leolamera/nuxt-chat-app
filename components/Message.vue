<template>
    <div :class="container">
        <h2 :class="username">{{author}}</h2>
        <h1 class="text-xl w-full whitespace-pre-line">{{message}}</h1>
        <h4 :class="time">16:20</h4>
    </div>
</template>
<script>
export default {
    props: ['botOrigin', 'author', 'message'],
    data() {
        return {
            container: !this.botOrigin ? "ml-12 mr-4 py-3 pl-3 pr-4 rounded-lg bg-gray-200 space-y-2" : "ml-4 mr-12 py-3 pl-4 pr-3 rounded-lg bg-gray-200 space-y-2",
            username: !this.botOrigin ? "text-right text-xl font-medium" : "text-left text-xl font-medium",
            time: !this.botOrigin ?  "text-right" : "text-left"
        }
    }
}
</script>