<template>
    <div>
        <h2>register</h2>
            <!-- v-model="inputText"  -->
            <span>입력:</span>
            <input 
                :value="inputText" 
                :placeholder="placeholder"
                type="text"
                @input="inputText = $event.target.value"
                @keypress.enter="doInsert" >
            <button @click="doInsert">Add</button>
    </div>
</template>
<script>
export default ({
    name: 'TodoRegister',
    data(){
        return {
            inputText:'',
            placeholder:'입력 바랍니다.'
        }
    },
    methods: {
        doInsert(){
            console.log(this.inputText);
            if(this.inputText != '') {
                this.$emit('doInsert', this.inputText)
            }
        }
    }
})
</script>
