<template>
    <li v-if="isEditMode" class="todo-item" >
        <input type="text" name="txt" v-model="edingText">
        <div class="btns">
            <button class="btn-update" @click="doUpdate(todo.key)">Update</button>
        </div>
    </li>
    <li v-else-if="!isEditMode" class="todo-item" >
        <input type="checkbox" v-model="todo.done">
        <span>{{todo.txt}}</span>
        <span>{{todo.updateDate}}</span>
        <div class="btns">
            <button class="btn-edit" @click="doEdit(todo.key)">Edit</button>
            <button class="btn-delete" @click="doDelete(todo.key)">Delete</button>
        </div>
    </li>
</template>
<script>

export default ({
    name:'TodoItem',
    props:{
        todo:Object
    },
    data () {
        return {
            isEditMode: false,
            edingText: this.todo.txt
        }
    },
    computed:{
        done:{
            get(){
                return this.todo.done
            },
            set(value){
                this.todo.done = value
            }
        }
    },
    methods:{
        doEdit(){
            this.isEditMode = true
            this.edingText = this.todo.txt
        },
        doDelete(){
            this.$emit('doDelete', this.todo)
        },
        doUpdate(){
            if(!!this.edingText && this.edingText != this.todo.txt){
                this.todo.txt = this.edingText
                this.todo.updateDate = new Date().toISOString()
                this.$emit('doUpdate', this.todo)
            }
            this.isEditMode = false
        }
    }
})
</script>
