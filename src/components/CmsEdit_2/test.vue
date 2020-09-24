<template>
    <div>
        <h1>CmsEdit- test:</h1>
        <hr />
        data : {{ data1 }}
        <hr />
        <button v-on:click="test1()">[ test1 ]</button>
        <hr />
        <button v-on:click="delete_items()">[ test_delete ]</button>
        <hr />

    </div>
</template>

<script>
import {Mixin} from '../../mixin'
import Dexie from 'dexie';
import LibCmsEdit_3 from '@/libs/LibCmsEdit_3';
import LibCommon from '@/libs/LibCommon';

var db = null;
//
export default {
    mixins:[Mixin],
    created() {
        var config = LibCmsEdit_3.get_const()
        db = new Dexie( config.DB_NAME );
        db.version( config.DB_VERSION).stores( config.DB_STORE );        
    },
    data() {
        return {
            user : [],
            message : "data: Hello-TestChild-123",
            child_data : "hoge",
            data1 : "",
        }
    },
    methods: {
        test1: function(){
            for(var i = 1; i<= 1000; i++){
                this.add_item(i)
            }
        },
        delete_items: function(){
            var s = "全てのデータを削除します。よろしいですか？バックアップが無い場合は復元できません"
            if(window.confirm(s)){
                db.cms_edit.clear()
                db.pages.clear()            
                db.category.clear()
                this.$router.push('/edit') 
            }
        },
        add_item: function(num){
console.log(num)
            var category = {
                id: 0,
                name: "",
                save_id: "id0",
            };
            var dt = LibCommon.formatDate( new Date(), "YYYYMMDDhhmmss");
            var task = {
//                category_id: 0,
                category: category,
                show_id: dt,
                title: "title-" + num,
                content: "title-",
                created_at: new Date(),
            }
            db.cms_edit.add( task)
        },
        get_items(){
            db.friends
                .toArray()
                .then(function (items ) {
                    console.log( items );
                });
        },

    }
}
</script>

