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
                    <div class="alert alert-danger" role="alert" v-if="!loading && !tasks.data.length">
                        There is no active task
                    </div>
                    <div class="spinner d-flex align-items-center justify-content-center" v-if="loading">
                        <div class="cube1"></div>
                        <div class="cube2"></div>
                    </div>
                    <div v-for="task in tasks.data" v-if="!loading">
                        <div class="form-check">
                            <input v-model="task.completed" class="form-check-input" :value="task.completed" true-value='1' false-value="0" type="checkbox" id="defaultCheck1">
                            <label class="form-check-label" for="defaultCheck1">
                                {{task.body}}
                            </label>
                        </div>
                    </div>
                    <nav aria-label="Page navigation example">
                        <ul class="pagination">
                            <li class="page-item">
                                <a class="page-link" @click="loadData(tasks.prev_page_url)" v-show='tasks.current_page - 1 != 0'>Previous</a>
                            </li>

                            <li v-for="i in 5" class="page-item">
                                <a class="page-link" @click="loadData(tasks.path + '?page=' + (tasks.current_page + i))">{{tasks.current_page + i}}</a>
                            </li>

                            <li class="page-item">
                                <a class="page-link" @click="loadData(tasks.next_page_url)" v-show='(tasks.current_page + 4) < tasks.last_page'>Next</a>
                            </li>
                        </ul>
                    </nav>
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
                loading:true,
                newTaskMessage:'',
                tasks:'',
            }
        },
        methods:{
            loadingStart(){this.loading = true},
            loadingStop(){this.loading = false},
            loadData(url = '/api/tasks'){
                this.loadingStart()
                axios.get(url)
                .then((response) => {
                    console.log(response.data)
                    this.tasks = response.data
                    this.loadingStop()
                })
                .catch((error) => {
                    console.log(error)
                    this.loadingStop()
                })
                console.log(this.paginationNumber)
            },
            newTask(newTaskMessage) {
                var task = {body:newTaskMessage, completed:false}
                axios.post('/api/tasks/', task)
                .then((response) => {
                    console.log(response.data)
                })
                .catch((error) => {
                    console.log(error)
                    this.loadingStop()
                })
                this.newTaskMessage = ''
                this.loadData()
            }
        },
        mounted(){
            this.loadData()
        }
    }
</script>

<style lang='scss' scoped>
.spinner {
  margin: 50px auto;
  width: 40px;
  height: 40px;
  position: relative;
}

.cube1, .cube2 {
  background-color: #333;
  width: 10px;
  height: 10px;
  position: absolute;
  top: 0;
  left: 0;
  
  -webkit-animation: sk-cubemove 1.8s infinite ease-in-out;
  animation: sk-cubemove 1.8s infinite ease-in-out;
}

.cube2 {
  -webkit-animation-delay: -0.9s;
  animation-delay: -0.9s;
}

@-webkit-keyframes sk-cubemove {
  25% { -webkit-transform: translateX(42px) rotate(-90deg) scale(0.5) }
  50% { -webkit-transform: translateX(42px) translateY(42px) rotate(-180deg) }
  75% { -webkit-transform: translateX(0px) translateY(42px) rotate(-270deg) scale(0.5) }
  100% { -webkit-transform: rotate(-360deg) }
}

@keyframes sk-cubemove {
  25% { 
    transform: translateX(42px) rotate(-90deg) scale(0.5);
    -webkit-transform: translateX(42px) rotate(-90deg) scale(0.5);
  } 50% { 
    transform: translateX(42px) translateY(42px) rotate(-179deg);
    -webkit-transform: translateX(42px) translateY(42px) rotate(-179deg);
  } 50.1% { 
    transform: translateX(42px) translateY(42px) rotate(-180deg);
    -webkit-transform: translateX(42px) translateY(42px) rotate(-180deg);
  } 75% { 
    transform: translateX(0px) translateY(42px) rotate(-270deg) scale(0.5);
    -webkit-transform: translateX(0px) translateY(42px) rotate(-270deg) scale(0.5);
  } 100% { 
    transform: rotate(-360deg);
    -webkit-transform: rotate(-360deg);
  }
}
</style>