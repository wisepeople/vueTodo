<template>
    <div>
        <todo-list v-model="db"></todo-list>
        <todo-register @doInsert="doInsert"></todo-register>
    </div>
</template>
<script>
import TodoList from './TodoList'
import TodoRegister from './TodoRegister'
import lowdb from 'lowdb'
import LocalStorage from 'lowdb/adapters/LocalStorage'
import _ from 'lodash'

export default ({
    name: 'TodoApp',
    components: {
        TodoList,
        TodoRegister
    },
    data(){
        return {
            db:null
        }
    },
    methods: {
        initDb(){
            const adaptor = new LocalStorage('wish-todo')
            this.db = lowdb(adaptor)
            this.db.defaults({
                list:[]
            }).write()
        },
        doInsert(txt){
            console.log('onApp');
            console.log('_list', _list);
            const _list = this.db.get('list');
            
            if(_list.findIndex(txt)==-1) {
                _list.push(txt)
                this.db.update('list', _list).write()
            }
            if()
        }
    },
    created() {
        this.initDb();
    }
})
</script>
