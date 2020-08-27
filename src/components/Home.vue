<template>
    <div>
        <h1>Home</h1>
        <p>Welcome, cms3edit-kuc</p> <br />
        <br />
        <br />
    </div>
</template>

<script>
import {Mixin} from '../mixin'

export default {
    mixins:[Mixin],
    created () {
        var url = this.get_exStorage( this.sysConst.KEY_NEXT_ACTION )
        this.remove_exStorage(this.sysConst.KEY_NEXT_ACTION )
//console.log("url="+ url );
        if(url.length > 0){
            this.$router.push( url )
        }
    }
}
</script>
