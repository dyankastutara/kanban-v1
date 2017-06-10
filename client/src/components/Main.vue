<template lang="html">
	<div class="layout">
		<div class="layout-content">
			<Row style="text-align:right; margin:15px;">
				<Col><h1>Kanban DyK</h1></Col>
			</Row>
			<hr>
			<Row style="margin:15px;">
				<Col>
					<Button type="success" style="border-radius:2px;" @click="showModalNewTask = true">
						<Icon type="plus-round"></Icon>New Task
					</Button>
					<Modal
					v-model="showModalNewTask"
					title="New Task"
					:loading="loading"
					ok-text="Save"
					cancel-text="Cancel"
					@on-cancel="asyncCancel"
					@on-ok="asyncSave">
					<div class="input">
						<p><strong>Title</strong></p>
						<p><Input v-model="title" placeholder="Task Title" ></Input></p>
					</div>
					<div class="input">
						<p><strong>Description</strong></p>
						<p><Input v-model="description" type="textarea" :rows="3" placeholder="Task Description"></Input></p>
					</div>
					<div class="input">
						<p><strong>Point</strong></p>
						<p><Input v-model="point"></Input></p>
					</div>
					<div class="input">
						<p><strong>Assigned To</strong></p>
						<p><Input v-model="assignedTo" placeholder="Assigned To"></Input></p>
					</div>
				</Modal>
			</Col>
		</Row>

		<Modal v-model="showModalSticky" width="500" >
			<p slot="header" style="color:#000;text-align:center">
				<Icon type="information-circled"></Icon>
				<span>{{task.title}} Assigned to {{task.assignedTo}}</span>
			</p>
			<div style="text-align:left">
				<p>Description : {{task.description}}</p>
				<p>Point : {{task.point}}</p>
			</div>
			<div slot="footer">
				<Button type="error" v-if="task.status>0" @click="updatePrev(task, task.status-1)">{{textPrev}}</Button>
				<Button type="warning" @click="deleteTask(task)">Delete</Button>
				<Button type="success" v-if="task.status<3" @click="updateNext(task, task.status+1)">{{textNext}}</Button>
			</div>
		</Modal>

		<Row style="margin : 10px;">
			<Col :xs="24" :sm="24" :md="6" :lg="6" style="margin : 0;">
				<Card :bordered="true" >
					<p slot="title">Back-Log</p>
					<p>
						<div class="card" v-for="(task,index) in tasks" v-if="task.status==0">
							<Card :bordered="true" >
								<p slot="title">{{task.title}}</p>
								<p>Point : {{task.point}}</p>
								<p>Assigned To : {{task.assignedTo}}</p>
								<p>
									<Button type="primary" style="border-radius:2px;" @click="showModal(task)">
										<Icon type="bookmark"></Icon>Detail Task
									</Button>
								</p>
							</Card>
						</div>
					</p>
				</Card>
			</Col>
			<Col :xs="24" :sm="24" :md="6" :lg="6" style="margin : 0;">
				<Card :bordered="true">
					<p slot="title">To-Do</p>
					<p>
						<div class="card" v-for="(task,index) in tasks" v-if="task.status==1">
							<Card :bordered="true" >
								<p slot="title">{{task.title}}</p>
								<p>Point : {{task.point}}</p>
								<p>Assigned To : {{task.assignedTo}}</p>
								<p>
									<Button type="primary" style="border-radius:2px;" @click="showModal(task)">
										<Icon type="bookmark"></Icon>Detail Task
									</Button>
								</p>
							</Card>
						</div>
					</p>
				</Card>
			</Col>
			<Col :xs="24" :sm="24" :md="6" :lg="6" style="margin : 0;">
				<Card :bordered="true">
					<p slot="title">Doing</p>
					<p>
						<div class="card" v-for="(task,index) in tasks" v-if="task.status==2">
							<Card :bordered="true" >
								<p slot="title">{{task.title}}</p>
								<p>Point : {{task.point}}</p>
								<p>Assigned To : {{task.assignedTo}}</p>
								<p>
									<Button type="primary" style="border-radius:2px;" @click="showModal(task)">
										<Icon type="bookmark"></Icon>Detail Task
									</Button>
								</p>
							</Card>
						</div>
					</p>
				</Card>
			</Col>
			<Col :xs="24" :sm="24" :md="6" :lg="6" style="margin : 0;">
				<Card :bordered="true">
					<p slot="title" >Done</p>
					<p>
						<div class="card" v-for="(task,index) in tasks" v-if="task.status==3">
							<Card :bordered="true" >
								<p slot="title">{{task.title}}</p>
								<p>Point : {{task.point}}</p>
								<p>Assigned To : {{task.assignedTo}}</p>
								<p>
									<Button type="primary" style="border-radius:2px;" @click="showModal(task)">
										<Icon type="bookmark"></Icon>Detail Task
									</Button>
								</p>
							</Card>
						</div>
					</p>
				</Card>
			</Col>
		</Row>
	</div>
	<div class="layout-copy">
		2017 &copy; Kanban DyK
	</div>
</div>
</template>

<script>
import * as firebase from 'firebase'

var config = {
	apiKey: "AIzaSyD8dO351rewNpA65VMpwEHOuVP1jrHZNas",
	authDomain: "post-product.firebaseapp.com",
	databaseURL: "https://post-product.firebaseio.com",
	projectId: "post-product",
	storageBucket: "post-product.appspot.com",
	messagingSenderId: "1072957595151"
};
const firebaseApp = firebase.initializeApp(config);
const db = firebaseApp.database();
const tasksRef = db.ref('tasks')
export default {
	data(){
		return {
			showModalNewTask : false,
			title : '',
			description :'',
			point : 0,
			assignedTo : '',
			status: 0,
			loading: true,
			task : {
				taskId : null,
				title : null,
				description :null,
				point : null,
				assignedTo : null,
				status: null
			},
			// tasks : {
			// 	taskId : null,
			// 	title : null,
			// 	description :null,
			// 	point : null,
			// 	assignedTo : null,
			// 	status: null
			// },
			textNext  : '',
			textPrev  : '',
			showModalSticky : false
		}
	},
	firebase: {
		tasks: tasksRef
	},
	methods : {
		showModal(task) {
			this.task = task
			this.showModalSticky = true
			this.prev()
			this.next()
		},
		next(){
			var self = this
			switch(self.task.status){
				case 0:
				self.textNext = 'To Do'
				break;
				case 1:
				self.textNext = 'Doing'
				break;
				case 2:
				self.textNext = 'Done'
				break;
				case 3:
				self.textNext = 'Done'
				break;
			}
		},
		prev(){
			var self = this
			switch(self.task.status){
				case 0:
				self.textPrev = 'Back Log'
				break;
				case 1:
				self.textPrev = 'Back Log'
				break;
				case 2:
				self.textPrev = 'To Do'
				break;
				case 3:
				self.textPrev = 'Doing'
				break;
			}
		},
		updateNext(task, newStatus){
			tasksRef.child(task['.key'])
			.child('status')
			.set(newStatus)
			this.showModalSticky = false
		},
		updatePrev(task, newStatus){
			tasksRef.child(task['.key'])
			.child('status')
			.set(newStatus)
			this.showModalSticky = false
		},
		deleteTask(task){
			this.$firebaseRefs.tasks.child(task['.key']).remove()
			this.showModalSticky = false
		},
		asyncSave(){
			var self = this
			let str = '0123456789';
			let length = 3;
			let result = '';
			for (let i = length; i > 0; i--) {
				result += str[Math.floor(Math.random() * str.length)];
			}
			var data = {
				taskId : result,
				title: self.title,
				description: self.description,
				point: self.point,
				assignedTo : self.assignedTo,
				status : self.status
			}
			tasksRef.push(data)
			setTimeout(() => {
				self.loading = false;
				self.showModalNewTask = false
				self.title = ''
				self.description =''
				self.point = 0
				self.assignedTo = ''
			}, 2000);
		},
		asyncCancel(){
			this.showModal = false
			this.title = ''
			this.description =''
			this.point = 0
			this.assignedTo = ''
		}
	}
}
</script>

<style scoped>
.layout{
	border : solid 1px #9ea7b4;
	margin-top : 10px;
	border-radius : 4px;
}
.layout-content{
	min-height: 200px;
	overflow: hidden;
	background: #fff;
	border-radius: 4px 4px 0px 0px;
	align-content: space-between;
}
.layout-copy{
	text-align: center;
	padding: 10px 0 20px;
	color: #9ea7b4;
	background: #f5f7f9;
	border-radius: 0px 0px 4px 4px;
}
.row-head{
	display : flex;
	//background: #f5f7f9;
	flex-wrap: wrap;
	flex-flow: row wrap;
	justify-content:space-between;
}
.input{
	padding-bottom : 15px;
}
</style>
