<template>
    <div class="modal-container">
        <div class="modal flex-center">
            <div class="modal-message">
                {{ message }}
            </div>
            <div class="actions flex-center">

                <slot name="actions">
                    <button @click="cancel">Tamam</button>
                </slot>


            </div>
        </div>
    </div>
</template>

<script>

    export default {
        name: 'Modal',
        props: {
            message: {
                type: String,
                required: true
            }
        },
        methods: {
            cancel(){
                this.$emit('cancel')
            }
        }
    }

</script>

<style lang="scss" scoped>

    .modal-container {

        height: 100%;
        width: 100%;
        background-color: rgba(0,0,0, .5);
        position: fixed;
        top: 0;
        left: 0;
        
        .modal {

            background-color: #fff;
            height: 300px;
            width: 400px;
            border-radius: 4px;
            padding: 10px;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            flex-direction: column;
            text-align: center;

            .modal-message {
                color: #000;
            }

            .actions {

                margin-top: 20px;

                button {
                    margin: 0 10px;
                }

                .btn-warning {

                    background-color: #ff6000;
                    border: 1px solid #ff6000;

                    &:hover {
                        color: #ff6000;
                        background-color: #fff;
                    }
                }

            }


        }
    }

</style>