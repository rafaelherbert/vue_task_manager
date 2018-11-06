<template>
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card card-default">
                <div class="card-header">
                    Task manager
                </div>
                
                <div class="card-body">
                    <div class="input-group mb-3">
                        <input v-model="newTaskMessage" @keyup.enter="newTask(newTaskMessage)" type="text" class="form-control" placeholder="Insert the message of your task." aria-label="Recipient's username">
                        <div class="input-group-append">
                            <button class="btn btn-outline-success" type="button" @click="newTask(newTaskMessage)">Button</button>
                        </div>
                    </div>

                    <div class="alert alert-danger" role="alert" v-show="!tasks.length">
                      There is no active task
                    </div>

                    <div v-for="task in tasks">
                        <div class="form-check">
                            <input @click='task.active = !task.active' class="form-check-input" :value="task.active" type="checkbox" id="defaultCheck1">
                            <label class="form-check-label" for="defaultCheck1">
                                {{task.message}}
                            </label>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>

<script>
    export default {
        data() {
            return {
                newTaskObj: {message:'', active:false},
                newTaskMessage:'',
                tasks:[]
            }
        },
        methods:{
            newTask(newTaskMessage) {
                var task = this.newTaskObj
                task.message = newTaskMessage
                this.tasks.push(task)
                this.newTaskMessage = ''
            },
        }
    }
</script>

<style lang='scss'>
</style>