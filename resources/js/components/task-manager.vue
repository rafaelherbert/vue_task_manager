<template>
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card card-default">
                <div class="card-header d-flex align-items-center justify-content-between">
                    Task manager
                    <nav aria-label="Page navigation example">
                        <ul class="pagination m-0">
                            <li class="page-item">
                                <a class="page-link" @click="loadData(tasks.prev_page_url)">Previous</a>
                            </li>
                            <li v-for="i in paginate_array" class="page-item" :class="{ 'active' : (i == tasks.current_page) }">
                                <a class="page-link" @click="loadData(tasks.path + '?page=' + i)">{{ i }}</a>
                            </li>
                            <li class="page-item">
                                <a class="page-link" @click="loadData(tasks.next_page_url)">Next</a>
                            </li>
                        </ul>
                    </nav>
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
                    <ul id="tasks" class="list-group list-group-flush">
                        <li class="list-group-item" v-for="task in tasks.data" v-if='!loading'>
                            <div class="form-check">
                                <input v-model="task.completed" @change="updateTask(task)" class="form-check-input" :value="task.completed" true-value='1' false-value="0" type="checkbox" :id="task.id">
                                <label class="form-check-label" :for="task.id">
                                    {{task.body}}
                                </label>
                            </div>
                        </li>
                    </ul>
                    
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
            updateTask(obj){
                console.log(obj.completed)
                axios.patch('/api/tasks/' + obj.id, obj)
                .then((response)=>{
                    console.log(response)
                })
                .catch((error) => {
                    console.log(error)
                })
                this.loadData()
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
            },
            range: function(min,max){
                var array = [],
                j = 0;
                for(var i = min; i <= max; i++){
                    array[j] = i;
                    j++;
                }
                return array;
            }
        },
        mounted(){
            this.loadData()
            /* 
            Jquery cannot select elements that use vue directives inside it, stick it in your mind.
            Keeping that on mind, you will need to use another library to do the drag and drop job, but remember
            it cant rely on jquery, because jquery and vue does NOT WORK PROPERLY TOGHETER, you cant select an element with jquery
            if it uses ANY VUE DIRECTIVE!!! IT works now, but need to be changed.
            */
            $('#tasks').sortable()
        },
        computed: {
            paginate_array: function() {
                // This function allows the pagination to be always on the center of the pagination interval.

                var pi = 5 // This is the pagination interval.
                var pi2 = pi - 1;
                var half_pi = Math.floor(pi/2);
                var start2, end2

                if (this.tasks.last_page < pi2){
                    start2 = 1
                    end2 = this.tasks.last_page
                } else if (this.tasks.current_page < pi2) {
                    start2 = this.tasks.current_page - half_pi > 0 ? this.tasks.current_page - half_pi : 1
                    end2 = start2 + pi2
                } else {
                    start2 = this.tasks.current_page + half_pi >= this.tasks.last_page ? this.tasks.last_page - pi2 : this.tasks.current_page - half_pi
                    end2 = this.tasks.current_page + half_pi >= this.tasks.last_page ? this.tasks.last_page : start2 + pi2
                }

                return this.range(start2, end2)
            }
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