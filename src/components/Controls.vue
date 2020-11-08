<template>
    <div class="wrapper flex-center">
        <div class="selections flex-center">
            <select :value="value" @change="seperatorChange">
                <option v-for="(option, index) in options" :key="index" :value="option.value">
                    {{option.label}}
                </option>
            </select>
            <button @click="save">
                Save
            </button>
        </div>
        <div class="feedback" v-if="feedback">
            {{feedback}}
        </div>
    </div>
</template>

<script>

    export default {
        name: 'Controls',
        props: {
            feedback: {
                type: String
            },
            save: {
                type: Function,
                required: true
            },
            value: {
                type: String,
                required: true
            }
        },
        data(){
            return {
                seperator: '',
                options: [
                    {
                        label: 'comma',
                        value: ','
                    },
                    {
                        label: 'tab',
                        value: '    '
                    },
                    {
                        label: 'semicolon',
                        value: ';'
                    },
                    {
                        label: 'pipe',
                        value: '|'
                    }
                ]
            }
        },
        methods: {
            seperatorChange(e){
                this.$emit('input', e.target.value)
            },
        }
    }
</script>

<style lang="scss" scoped>

    .wrapper {

        flex-direction: column;
        margin-bottom: 10px;

        .selections {

            margin-bottom: 10px;

            select {

                padding: 5px 10px;
                border: 1px solid #dbdbdb;
                margin-right: 20px;

            }

        }

        .feedback {
            color: red;
            font-weight: bold;
        }
    }

</style>