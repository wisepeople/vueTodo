<template>
    <div>
        <!-- <todo-list v-model="todoList"></todo-list> -->
        <div>
            <h3>list</h3>
            <ul class="menuFilter">
                <li><button :class="{active: filterKey=='all'}" @click="changeFilter('all')">All({{total}})</button></li>
                <li><button :class="{active: filterKey=='active'}" @click="changeFilter('active')">Not Yet({{activeCount}})</button></li>
                <li><button :class="{active: filterKey=='complete'}" @click="changeFilter('complete')">Completed({{completeCount}})</button></li>
            </ul>
            <ul>
                <todo-item 
                    v-for="todo in filteredList"
                    :key="todo.key"
                    :todo="todo"
                    v-on="$listeners"
                    @doUpdate="doUpdate"
                    @doDelete="doDelete"></todo-item>
            </ul>
            <div class="btns"><button id="btnClear" @click="doClear">clear</button></div>
        </div>
        <todo-register @doInsert="doInsert"></todo-register>
    </div>
</template>
<script>
import TodoList from './TodoList'
import TodoItem from './TodoItem'
import TodoRegister from './TodoRegister'
import lowdb from 'lowdb'
import LocalStorage from 'lowdb/adapters/LocalStorage'
import _ from 'lodash'
import { v4 as uuidv4 } from 'uuid'

export default ({
    name: 'TodoApp',
    components: {
        TodoList,
        TodoItem,
        TodoRegister
    },
    data(){
        return {
            db:null,
            todoList:[],
            filterKey:'all'
        }
    },
    computed:{
        filteredList(){
            switch(this.filterKey){
                case 'active':
                    return _.filter(this.todoList, {done:false})
                case 'complete':
                    return _.filter(this.todoList,{done:true})
                case 'all':
                default:
                    return this.todoList
            }
        },
        total(){
            return this.todoList.length
        },
        activeCount(){
            return _.filter(this.todoList, {done:false}).length
        },
        completeCount(){
            return _.filter(this.todoList, {done:true}).length
        }
    },
    methods: {
        initDb(){
            console.log('initDb')
            const adaptor = new LocalStorage('wish-todo')
            this.db = lowdb(adaptor)
            const _hasList = this.db.has('list').value()
            if(_hasList){
                this.todoList = _.cloneDeep(this.db.getState().list)
            } else {
                this.db.defaults({
                    list:[]
                }).write()
            }
        },
        getList(){
            return this.db.get('list').value()
        },
        _createItem(txt){
            const _time = new Date().toISOString()
            // uuid => cryptoRandomString도 고려
            const item = {
                id:uuidv4(),
                txt:txt,
                createDate:_time,
                updateDate:_time,
                done:false
            };
            return item
        },
        doInsert(txt){
            console.log('doInsert')
            // console.log('_list', _list)
            // const _list = this.db.get('list')
            
            const _item = this._createItem(txt)
            this.db
                .get('list')
                .push(_item)
                .write()
            this.todoList = _.cloneDeep(this.db.getState().list)
            return _item.id
        },
        doUpdate(todo) {
            console.log('doUpdate', arguments)
            const _item = this.db
                .get('list')
                .find({id: todo.id})
                // .set('txt',txt)
                .assign(todo)
                .write()
            // this.todoList = _.cloneDeep(this.db.getState().list)    //부하많이 걸릴지도
            _.assign(_.find(this.todos, {id:todo.id}), todo)
        },
        doDelete(todo) {
            console.log('doDelete', arguments)
            this.db.get('list').remove({id:todo.id}).write()
            // this.todoList = _.cloneDeep(this.db.getState().list)
            const _idx = _.findIndex(this.todoList, {id:todo.id})
            _.remove(this.todoList, {id:todo.id})
            this.$delete(this.todoList, _idx)
        },
        doClear() {
            // this.db.get('list').update([]).write()
            this.db.set('list', []).write()
            this.todoList = _.cloneDeep(this.db.getState().list)
            // this.$delete(this.todoList, 0) 모든 row를 호출하는건 비효율적
        },
        changeFilter(key) {
            this.filterKey = key;
        }
    },
    created() {
        this.initDb()
    }
})
</script>
<style scoped>

    ul.menuFilter>li{
        display: inline-block;
        width:30%;
    }
    ul.menuFilter>li>button{
        width:100%;
        height: 30px;
        background-color:#fff;
        border: solid 0px;
    }
    ul.menuFilter>li>button.active{
        font-weight: 400;
        color:#fff;
        background-color:rgb(20, 20, 58);
    }
</style>