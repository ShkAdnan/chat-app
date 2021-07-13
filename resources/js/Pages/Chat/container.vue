<template>
    <app-layout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                <ChaTRoomSelection 
                    v-if="currentRoom.id"
                    :rooms="chatRooms"
                    :currentRoom="currentRoom"
                    v-on:roomChanged="setRoom( $event )"
                />
            </h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg">
                   <message-container :messages="messages" />
                   <input-message 
                    :room="currentRoom" 
                    v-on:messageSent="getMessages()"
                    />
                </div>
            </div>
        </div>
    </app-layout>
</template>

<script>
    import AppLayout from '@/Layouts/AppLayout'
    import MessageContainer from './messageContainer.vue'
    import inputMessage from './inputMessage.vue'
    import ChaTRoomSelection from './chatRoomSelection.vue'

    export default {
        components: {
            AppLayout,
            MessageContainer,
            inputMessage,
            ChaTRoomSelection
      
        },
        data : function(){
            return {
                chatRooms   : [],
                currentRoom : [],
                messages     : []
            }
        },
        watch: {
            currentRoom( val, oldVal){
                if( oldVal ){
                    this.disconnect( oldVal );
                }
                this.connect();
            }
        },
        methods : {
            connect(){
                if( this.currentRoom.id ){
                    let vm = this;
                    this.getMessages();
                    window.Echo.private("chat." + this.currentRoom.id )
                    .listen('.message.new', e => {
                        vm.getMessages();
                    })
                }
            },
            disconnect( room ){
                window.Echo.leave("chat." + room.id );
            },
            getRooms(){
                axios.get('/chat/rooms')
                .then(res => {
                    this.chatRooms = res.data;
                    this.setRoom( res.data[0] );
                })
                .catch(err => console.log(err))
            },
            setRoom( room ){
                this.currentRoom = room;
            },
            getMessages(){
                axios.get('/chat/room/'+ this.currentRoom.id + '/messages')
                .then( res => {
                    this.messages = res.data
                })
                .catch(err => console.log(err) )
            }
        },
        created() {
            this.getRooms();
        }
    }
</script>
