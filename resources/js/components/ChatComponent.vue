<template>
	<div class="chat card">
		<div class="scrollable card-body" ref="hasScrolledToBottom">
	        <div v-for="(message,index) in messages" :key="index">
	            <div class="message message-receive" v-if="user.id != message.user.id">
	                <p>
	                    <strong class="primary-font">
	                        {{ message.user.name }} :
	                    </strong>
	                    {{ message.message }}
	                </p>
	            </div>
	            <div class="message message-send" v-else>
	                <p>
	                    <strong class="primary-font">
	                        {{ message.user.name }} :
	                    </strong>
	                    {{ message.message }}
	                </p>
				
	            </div>
	        </div>
	    </div>
	    <div class="chat-form input-group">
	        <input id="btn-input" 
			type="text" 
			name="message" 
			class="form-control input-sm message-" 
			placeholder="Type your message here..." 
			v-model="newMessage" 
			@keyup.enter="addMessage"
			@focus="onFocus"
			@blur="onBlur"
			>

	        <span class="input-group-btn">
	            <button class="btn btn-primary" id="btn-chat" @click="addMessage">
	                Send
	            </button>
	        </span>
	    </div>

	</div>
</template>
<script>
	import { reactive, inject,ref, onMounted,onUpdated } from 'vue';

	import axios from 'axios';
	export default{
		props:['user'],
		// data(){
		// 	return {
		// 		messages:[],
		// 		newMessage:'',
		// 		hasScrolledToBottom:'',
		// 	}
		// },
		// created(){
		// 	this.fetchMessages();
		// 	console.log(this.user);
			
		// },
		// methods:{
		// 	async fetchMessages(){
		// 		await axios.get('/messages').then(response => {
	    //             this.messages = response.data;
	    //         });
		// 	},

		// 	async addMessage(){
		// 		let user_message = {
        //             user: this.user,
        //             message: this.newMessage
        //         };
		// 		 this.messages.push(user_message);
	    //         await axios.post('/message-create', user_message).then(response => {
	    //           console.log(response.data);
	    //         });
        //         this.newMessage = ''
		// 		Echo.private('chat-channel')
		// 		.listen('SendMessage', (e) => {
		// 			this.messages.push({
		// 			message: e.message.message,
		// 			user: e.user
		// 			});
		// 		})
		// 	},

		// 	scrollBottom(){
		// 		if(this.messages.length > 1){
		//         	let el = this.hasScrolledToBottom;
	    //   			el.scrollTop = el.scrollHeight;
	    //     	}  
		// 	},
			
		// },
		
	    setup(props){
	    	let messages = ref([])
	    	let newMessage = ref('')
	    	let hasScrolledToBottom = ref('')

	    	onMounted(() =>{
	    		fetchMessages();
			console.log(messages);

	    	})
	    	onUpdated(() => {
	    		scrollBottom()
	    	})


	    	Echo.private('chat-channel')
				.listen('SendMessage', (e) => {
					messages.value.push({
					message: e.message.message,
					user: e.user
					});
				})

	    	const fetchMessages = async()=> {
	            axios.get('/messages').then(response => {
	                messages.value = response.data;
	            });
	        }
	        const addMessage = async()=> {
	        	let user_message = {
                    user: props.user,
                    message: newMessage.value
                };
	            messages.value.push(user_message);
	            axios.post('/message-create', user_message).then(response => {
	              console.log(response.data);
	            });
                newMessage.value = ''
	        }
	        const scrollBottom = () =>{
	        	if(messages.value.length > 1){
		        	let el = hasScrolledToBottom.value;
	      			el.scrollTop = el.scrollHeight;
	        	}        	
	        }
	        return {
	        	messages,
	        	newMessage,
	        	addMessage,
	        	fetchMessages,
	        	hasScrolledToBottom,
	        }
	    } 
	}
</script>