<template>
    <div style="position: relative;margin:2px;display:flex;justify-content:space-between;">
        <div>
            <P>压手，腿带  =</P>
            <P>手支架  →</P>
            <P>负极板  □</P>
            <P>止血带  +</P>
            <P>输液部位  △</P>
            <P>引流管  ○</P>
        </div>
        <img src="../../assets/body.jpg" style="height:100%;display:block;">
    </div>
</template>
<script>
import Bus from '@/bus.js';
export default {
    data() {
        return {
            
        }
    },
    props: ['page','value'],
}
</script>
<style scoped>

</style>
