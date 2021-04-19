<template>
    <li v-if="isEditMode" class="todo-item" >
        <input type="text" name="txt" :value="editingText" @input="editingText = $event.target.value">
        <div class="btns">
            <button class="btn-update" @click="doUpdate">Update</button>
        </div>
    </li>
    <li v-else-if="!isEditMode" class="todo-item" >
        <input type="checkbox" v-model="done">
        <span>{{todo.txt}}</span>
        <span>{{date}}</span>
        <div class="btns">
            <button class="btn-edit" @click="doEdit">Edit</button>
            <button class="btn-delete" @click="doDelete">Delete</button>
        </div>
    </li>
</template>
<script>
// import dayjs from 'dayjs'
import moment from 'moment'
export default ({
    name:'TodoItem',
    props:{
        todo:Object
    },
    data () {
        return {
            isEditMode: false,
            editingText: this.todo.txt
        }
    },
    computed:{
        done:{
            get(){
                return this.todo.done
            },
            set(value){
                this.todo.done = value
                this.$emit('doUpdate', this.todo) 
            }
        },
        date(){
            if(this.todo.updateDate == this.todo.createDate) {
                return this._getTerm(this.todo.createDate)
            } else {
                const _date = moment(this.todo.updateDate)
                // return `${_date.format('YYYY/MM/DD HH:mm:ss')} Modified.`
                return this._getTerm(this.todo.updateDate, ' Modified.')
            }
        }
    },
    methods:{
        _getTerm(_dateStr, modifiedStr=''){
            const createdAt = new Date(_dateStr)
            const milliSeconds = new Date() - createdAt
            const seconds = milliSeconds / 1000
            if (seconds < 60) return `방금 전` + modifiedStr
            const minutes = seconds / 60
            if (minutes < 60) return `${Math.floor(minutes)}분 전` + modifiedStr
            const hours = minutes / 60
            if (hours < 24) return `${Math.floor(hours)}시간 전` + modifiedStr
            const days = hours / 24
            if (days < 7) return `${Math.floor(days)}일 전` + modifiedStr
            const weeks = days / 7
            if (weeks < 5) return `${Math.floor(weeks)}주 전` + modifiedStr
            const months = days / 30
            if (months < 12) return `${Math.floor(months)}개월 전` + modifiedStr
            const years = days / 365
            return `${Math.floor(years)}년 전` + modifiedStr
        },
        doEdit(){
            this.isEditMode = true
            this.editingText = this.todo.txt
        },
        doDelete(){
            this.$emit('doDelete', this.todo)
        },
        doUpdate(){
            if(!!this.editingText && this.editingText != this.todo.txt){
                this.todo.txt = this.editingText
                this.todo.updateDate = new Date().toISOString()
                this.$emit('doUpdate', this.todo)
            }
            this.isEditMode = false
        },
        doComplete(){
            this.todo.done = !this.todo.done
            this.$emit('doUpdate', this.todo)   //내용은 done의 갱신이므로
        }
    }
})
</script>
