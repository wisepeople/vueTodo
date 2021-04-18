<template>
    <div>
        <h3>list</h3>
        <ul>
            <todo-item 
                v-for="todo in todoList"
                :key="todo.key"></todo-item>
        </ul>
    </div>
</template>
<script>
import TodoItem from './TodoItem'
export default ({
    name: 'TodoList',
    data(){
        return {
            todoList
            }
    },
    props:{
        todoList:Array
    },
    computed:{

    },
    method:{
        doEdit(key){

        },
        doDelete(key){

        }
    },
    components:{
        TodoItem
    }
})
</script>
