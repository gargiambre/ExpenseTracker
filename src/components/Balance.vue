<template>
    <h2>Your balance</h2>
    <h1 id="Balance"> Rs {{ total}}</h1>
</template>

<script setup>
    import {defineProps} from 'vue';

    const props = defineProps
    ({
        total:
        {
            type: Number,
            required: true,
        }
    })
    

</script>