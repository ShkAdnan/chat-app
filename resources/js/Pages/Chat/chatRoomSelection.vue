<template>
   <div class="grid grid-cosl-2">
        <div class="font-bold text-xl">
            {{ selected.name }} Chat
        </div>
        <div>
            <select 
                v-model="selected"
                @change="$emit('roomChanged' , selected)"
                class="float-right">

                <option
                    v-for="room in rooms" :key="room.id"
                    :value="room">
                        {{ room.name }}
                </option>
            </select>
        </div>
    </div>   
</template>

<script>

export default ({
    props : ['rooms' , 'currentRoom'],
    data : () => {
        return {
            selected : ''
        }
    },
    created(){
         this.selected = this.currentRoom;
    }
})
</script>
