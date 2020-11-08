<template>

    <div class="table-view flex-center">

        <div class="inner flex-center">

            <div class="top-actions flex-center">
                <div class="feedback"> {{ feedback }} </div>
                <button @click="checkEmails" :disabled="selectsFilled" class="save-btn">
                    Save
                </button>
            </div>


            <table>

                <tr>
                    <th v-for="(tableOption, colIndex) in tableOptions" :key="colIndex">
                        <Select 
                            @change="selectChange" 
                            :tableOptions="tableOptions" 
                            :colIndex="colIndex"
                        />
                    </th>
                </tr>

                <tr 
                    v-for="(rows, index) in pagedData" 
                    :key="index" 
                    :class="{'errors': invalidRowKeys.indexOf(index) != -1 }"
                >
                    <td v-for="(row, rowIndex) in rows" :key="rowIndex">
                        {{ row }}
                    </td>
                </tr>

            </table>

            <div class="actions">
                <div class="left">
                    <button @click="goBack">
                        Go back
                    </button>
                </div>
                <div class="right">
                    <button @click="prevPage" :disabled="currentPage == 1">
                        Previous
                    </button>
                    <button @click="nextPage" :disabled="currentPage == maxPage">
                        Next
                    </button>
                </div>
            </div>

            
        </div>

        <Modal v-if="warning" :message="message" @cancel="warning = false">

            <template v-slot:actions v-if="finalStep">

                <button @click="warning = false" class="btn-warning">
                    Geri dön
                </button>
                <button @click="submitTable">
                    Devam et
                </button>

            </template>


        </Modal>

    </div>

</template>

<script>

    import Modal from './Modal'
    import Select from './Select'

    export default {
        name: 'Table',
        components: {
            Modal,
            Select
        },
        props: {
            entries: {
                type: String,
                required: true
            }
        },
        data(){
            return {
                currentPage: 1,
                invalidEmails: [],
                warning: false,
                message: '',
                feedback: 'Please pick column headings to continue',
                values: {
                    email: []
                },
                invalidRowKeys: [],
                finalStep: false,
                selectsFilled: true,
                tableOptions: [
                    {
                        label: 'E-mail',
                        value: 'email'
                    },
                    {
                        label: 'Name',
                        value: 'name'
                    },
                    {
                        label: 'Surname',
                        value: 'surname'
                    },
                    {
                        label: 'Birth Date',
                        value: 'birthDate'
                    }
                ]
            }
        },
        computed: {
            tableData(){
                return this.entries.split('\n').map(entry => {
                    return entry.split('\t')
                })
            },
            pagedData(){
                return this.tableData.slice((this.currentPage - 1) * 100, this.currentPage * 100)
            },
            maxPage(){
                return Math.ceil(this.tableData.length / 100)
            },
            isOverByPercent(){
                return parseInt(this.invalidEmails.length * 100 / this.entries.split('\n').length) > 50
            }
            
        },
        methods: {
            isValidColumns(){

                let optionValues = []

                document.querySelectorAll(".column-select").forEach(element => {
                    optionValues.push(element.value)
                })
                
                if(optionValues.indexOf("") == -1){

                    let status = true

                    optionValues.forEach(item => {
                        
                        const len = optionValues.filter(filterItem => {
                            return item == filterItem
                        })

                        if(len.length > 1){
                            status = false
                        }

                    })

                    if(status) return true
                    else return false
                    
                } else return false

            },
            checkEmails(){

                this.invalidEmails = []
                this.checkRule(this.values.email)

                if(this.isOverByPercent) {
                    this.warning = true
                    this.message = 'Yüklenen email adreslerinin yarısından fazlasında hata bulunmaktadır. Düzenleme yapıp yeniden yükleme yapabilirsiniz.'
                    return false
                } else if (!this.isOverByPercent && this.invalidEmails.length >= 1){
                    this.message = `Yüklenen email adreslerinin ${this.invalidEmails.length} tanesinde hata bulunmaktadır. Hatalı olanları yüklemeden devam etmek istiyor musunuz?`
                    this.warning = true
                    this.finalStep = true
                    return false
                }

                if(this.values.email.length != 0){
                    this.submitTable()
                } 
                
            },
            selectChange(colIndex, value){

                this.invalidEmails = []
                this.invalidRowKeys = []
                
                this.values[value] = this.tableData.map((item) => {
                    return item[Object.keys(item)[colIndex]]
                })


                if(!this.isValidColumns()){
                    this.selectsFilled = true
                } else {
                    this.selectsFilled = false
                }

            },
            checkRule(emails) {

                const rule = /^(([^<>()\]\\.,;:\s@"]+(\.[^<>()\]\\.,;:\s@"]+)*)|(".+"))@(([0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/

                if(emails){

                    emails.forEach((email, emailRowKey) => {
    
                        if(!rule.test(email)) { 
                            this.invalidEmails.push(email)
                            this.invalidRowKeys.push(emailRowKey)
                        } 
    
                    })

                    return this.invalidEmails

                } else {
                    return []
                }

            },
            nextPage(){
                this.currentPage += 1
            },
            prevPage(){
                this.currentPage -= 1
            },
            submitTable(){

                this.finalStep = false
                this.warning = false

                if(this.isValidColumns()){

                    const keys = Object.keys(this.values)

                    const structuredData = this.values['email'].map((email, index) => {
                        return keys.reduce((finalItem, currentKey) => {
                            return {
                                ...finalItem, 
                                [currentKey]: this.values[currentKey][index]
                            }
                        }, {})
                    })

                    console.log(structuredData)

                    // Request fake olduğu için bu şekilde yaptım. Formatlanmış JSON veriyi console'da görebilirsiniz.

                    this.warning = true
                    this.message = `${structuredData.length} üye girişi başarıyla yapılmıştır. Console'da örnek obje vardır.`
                    this.invalidEmails = []

                }

            },
            goBack(){
                this.$emit('goBack')
            }
        }
    }
    
</script>

<style lang="scss" scoped>

    .table-view {

        width: 100%;

        .inner {

            width: 50%;
            padding: 20px;
            flex-direction: column;

            .top-actions {

                flex-direction: row;
                justify-content: flex-end;
                width: 100%;

                .feedback {
                    font-size: 14px;
                    color: #ff6000;
                    margin: 0 auto;
                }

                .save-btn {




                    &:disabled {

                        background-color: #dedede;
                        border: 1px solid #dedede;
                        color: #000;
                        cursor: unset;

                    }
                }
            }



            button {

                margin: 20px 0;
                align-self: flex-end;

            }

            .actions {

                display: flex;
                align-items: center;
                justify-content: space-between;
                width: 100%;

                .left {

                    button {

                        background-color: #ff6000;
                        border: 1px solid #ff6000;

                        &:hover {
                            color: #ff6000;
                            background-color: #fff;
                        }

                    }

                }

                .right {

                    button {
                        margin-left: 10px;
                        background-color: #00aeef;
                        color: #fff;
                        border: 1px solid #00aeef;

                        &:hover {
                            color: #00aeef;
                            background-color: #fff;
                        }

                        &:disabled {
                            background-color: #dedede;
                            border: 1px solid #dedede;
                            color: #000;
                            cursor: unset;
                        }
                    }

                }
            
            }

            .errors {
                background-color: #ffeef0
            }

        } 

        table, td, th {
            border: 1px solid black;
        }

        th {
            font-weight: bold;
            background-color: #1da1f2;
            color: #fff;
        }

        td, th {
            padding: 5px;
            font-size: 12px;
            text-align: center;
        }

        table {
            border-collapse: collapse;
            width: 100%;
            height: auto;
        }


    }


</style>