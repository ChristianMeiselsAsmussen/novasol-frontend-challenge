<template>
    <div id="app">
        <ns-form></ns-form>
    </div>
</template>

<script>
    import Form from './components/form';

    export default {
        name: 'app',
        components: {
            'ns-form': Form,
        }
    }
</script>

<style lang="scss">
    @import "~styles/settings";
    @import "~styles/global";

    html,
    body {
        height: 100%;
        margin: 0;
    }

    body {
        font-size: 16px;
        background-color: #F3F4F5;
        cursor: default;
        font-family: $font-opensans;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        color: #2c3e50;
        background: linear-gradient(-120deg, #FFB88C, #DE6262);
        background-size: cover;
        background-attachment: fixed;
    }

    #app {
        @include container;
        min-height: 100%;

        display: flex;
        justify-content: center;
        align-items: center;
    }

</style>
