<template>

    <div class="container flex-center">

        <template v-if="!nextStage">

            <Controls :feedback="feedback" :save="checkLines" v-model="seperator" class="controls"/>

            <div class="textarea-container">
                <div class="line-counter">
                    <div class="lines" ref="lines">
                        <p v-for="(line, index) in lines" :key="index">
                            {{ line }}
                        </p>
                    </div>
                </div>
                <textarea 
                    v-model="entries"
                    :class="['textarea']" 
                    :placeholder="placeholder"
                    @scroll="scroll"
                    ref="textarea"
                    :disabled="!seperator"
                    @paste="onPaste"
                    @input="onInput"
                >
                </textarea>
            </div> 

        </template>

        <Table :entries="entries" v-if="nextStage" @goBack="nextStage = false"/>


    </div>

</template>

<script>

    import Controls from './Controls'
    import Table from './Table'

    export default {
        name: 'TextArea',
        components: {
            Controls,
            Table
        },
        data(){
            return {
                placeholder: 'Please pick a seperator from the menu above',
                scrollDistance: 0,
                maxLines: 10000,
                seperator: '',
                feedback: '',
                entries: '',
                nextStage: false
            }
        },
        computed: {
            lines(){
                return this.entries.split('\n').length
            }
        },
        watch: {
            seperator(seperator){
                this.placeholder = `email${seperator}name${seperator}surname${seperator}dateOfBirth`
            }
        },
        methods: {
            scroll(e) {
                this.$refs.lines.style.transform = `translateY(${-e.target.scrollTop}px)`;
            },
            seperatorChange(seperator){
                this.seperator = seperator
            },
            checkLines(){
                if(!this.entries.length) return this.feedback = 'The text area can\'t be empty'
                if(this.lines.length > 10000) return this.feedback = 'You have exceeded the limit'

                this.nextStage = true

            },
            onInput(e){
                e.preventDefault()
                this.feedback = ''
            },
            onPaste(event){

                event.preventDefault()
                this.feedback = ''

                let paste = (event.clipboardData || window.clipboardData).getData('text')

                const lines = paste.split('\n').map(item => {

                    const subItem = item.split(this.seperator) 

                    subItem.length = 4

                    return subItem.join('\t')

                }).join('\n')

                this.entries = this.entries + lines

            }
        }
    }

</script>

<style lang="scss" scoped>

    .container {

        height: auto;
        flex-direction: column;

        .controls {
            margin-top: 200px;
        }

        .textarea-container {

            display: flex;
            font-size: 13px;
            width: 50%;
            line-height: 150%;
        
            .line-counter{

                position: relative;
                background-color: #F0F0F0;
                border: 1px solid #dbdbdb;
                color: green;
                padding: 15px;
                border-radius: 10px 0 0 10px;
                overflow: hidden;
                width: 60px;

                .lines {
                    position: absolute;
                }

            }

            .textarea {

                padding: 15px;
                width: 100%;
                height: 300px;
                border-left-width: 0;
                border: 1px solid #dbdbdb;
                outline: none;
                line-height: inherit;
                white-space: pre;
                resize: none;

                &::placeholder {
                    text-align: center;
                }

            }

        }
    }

</style>