<template>

    <select @change="change" class="column-select">
        <option value="">Select</option>
        <option 
            v-for="(item, innerIdx) in tableOptions" 
            :key="innerIdx" 
            :value="item.value"
        >
            {{item.label}}
        </option>
    </select>

</template>

<script>

    export default {
        name: 'Select',
        props: {
            tableOptions: {
                type: Array,
                required: true
            },
            colIndex: {
                type: Number
            }
        },
        methods: {
            change(e){
                this.$emit('change', this.colIndex, e.target.value)
            }
        }
        
    }

</script>

<style lang="scss" scoped>

</style>