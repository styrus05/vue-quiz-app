<template>
    <div>
        <b-nav tabs>
            <b-nav-item disabled><b>Fancy quiz app</b></b-nav-item>
            <b-nav-item disabled>Counter: {{counter}}/10</b-nav-item>
        </b-nav>
    </div>
</template>

<script>

export default {
    
    props: {
        index: Number
    },
    computed:{
        counter(){
            let counter = this.index + 1;
            return counter;
        }
    }
}
</script> 
