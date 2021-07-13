<template>
    <div>
        {{ message.user.name }} : {{ message.messages }}
    </div>    
</template>

<script>

export default ({
    props : ['message']
})
</script>
