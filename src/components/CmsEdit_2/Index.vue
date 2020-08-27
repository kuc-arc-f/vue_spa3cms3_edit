<template>
<div class="task_index_wrap">
    <div class="container">
        <FlashMessage></FlashMessage>
        <div class="row" style="margin-top: 10px;">
            <div class="col-sm-4"><h3>CmsEdit - ver3</h3>
            </div>
            <div class="col-sm-4">
                <router-link :to="'/edit/new/'" class="btn btn-primary">Create
                </router-link>
            </div>
            <div class="col-sm-4" style="text-align: left;">
                <a id="download" href="" download="cms.json" class="btn btn-outline-primary btn-sm"
                v-on:click="export_task()">Export
                </a>                
                &nbsp;
                <a href="" v-on:click="move_action('/edit/import');"
                    class="btn btn-outline-primary btn-sm">Import
                </a>                 
                &nbsp;
                <button v-on:click="delete_items();"
                    class="btn btn-outline-danger btn-sm ml-2">Delete
                </button>                 
            </div>
        </div>
        <hr class="mt-2 mb-2" />
        <div class="category_select_wrap">
            <div class="row">
                <div class="col-sm-6">
                </div>
                <div class="col-sm-6">
                    <div class="form-group mb-0">
                        <div class="col-sm-8">
                            <select v-model="category_save_id" required="required"  class="form-control"
                            id="category-save-id">
                                <option value="0">全てのカテゴリ</option>
                                <option v-for="item in category_items" v-bind:key="item.save_id"
                                    v-bind:value="item.save_id"> {{item.name}}
                                </option>
                            </select>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <!-- <hr class="mt-2 mb-2" /> -->
        <table class="table table-hover mt-2">
            <thead>
            <tr>
                <th>Title</th>
                <th>
                    Actions
                </th>
            </tr>
            </thead>            
            <tbody>
                <tr v-for="item in cms_items" v-bind:key="item.id">
                    <td>
                        <h3>
                        <router-link :to="'/edit/show/' + item.id">{{ item.title }}</router-link>
                        </h3>                        
                        ID : {{ item.id }}
                        , {{ item.created_at }}
                        , Category : {{ item.category.name }}
                    </td>
                    <td>
                        <router-link :to="'/edit/edit/' + item.id"
                            class="btn btn-outline-primary btn-sm">Edit
                        </router-link>                        
                    </td>
                </tr>
            </tbody>
        </table>
        <br />
    </div><!-- end_container -->
            
</div>
</template>

<!-- -->
<style> 
.page_info_wrap{
    background: #EEE;
    padding : 10px;
    margin-top : 20px;
}
</style>

<!-- -->
<script>
import {Mixin} from '../../mixin'
import FlashMessage from '../../components/Layouts/FlashMessage'
import Dexie from 'dexie';
import LibDexie from '@/libs/LibDexie';
import LibCmsEdit_3 from '@/libs/LibCmsEdit_3';
// import navbar from '@/components/Layouts/Navbar'

var db = null;
//
export default {
    mixins:[Mixin],
    components: { FlashMessage},
    created () {
console.log( "#-created" )
        var self = this
        var config = LibCmsEdit_3.get_const()
        db = new Dexie( config.DB_NAME );
        db.version( config.DB_VERSION).stores( config.DB_STORE ); 
        window.addEventListener("load", function() {
            window.document.getElementById("category-save-id").addEventListener("change", function() {
                self.change_category()
            });
        });        
        this.get_items()
    },
    data () {
        return {
            cms_items: [],
            items_org: [],
            category_items: [],
            page_items: [],
            category_save_id: 0,
            items_all : [],
        }
    },
    methods: {
        get_items(){
            var self = this
            db.cms_edit.toArray().then(function (items ) {
                self.cms_items = LibDexie.get_reverse_items(items)
                self.items_all = self.cms_items
// console.log( items )
            });
            db.cms_edit.toArray().then(function ( data ) {
                self.items_org = data
// console.log( self.items_org )
            });       
            db.category.toArray().then(function ( data ) {
                self.category_items = data
// console.log( self.category_items )
            }); 
            db.pages.toArray().then(function ( data ) {
                self.page_items = data
//console.log( self.page_items )
            });       
        },
        export_task: function(){
            var config = LibCmsEdit_3.get_const()
            var dt = new Date()
            var data = {
                save_date: dt, 
                file_version: config.file_version , 
                items: this.items_org, 
                category_items: this.category_items, 
                page_items: this.page_items, 
            }
            var content = JSON.stringify( data  );
//console.log( dt )
            var blob = new Blob([ content ], { "type" : "application/json" });

            var fname = "cms.json"
            if (window.navigator.msSaveBlob) { 
                console.log("#-msSaveBlob")
                window.navigator.msSaveBlob(blob, fname ); 
                window.navigator.msSaveOrOpenBlob(blob, fname ); 
            } else {
                console.log("#-msSaveBlob-false")
                document.getElementById("download").href = window.URL.createObjectURL(blob);
            }            
        },
        move_action: function( action  ){
            this.set_exStorage(this.sysConst.KEY_NEXT_ACTION , action )
            window.location.href = this.sysConst.HTTP_URL
        },
        change_category:function (){
// console.log( "#-change_category:" + this.category_save_id )
            if(this.category_save_id == "0"){
                this.cms_items = this.items_all
            }else{
                this.cms_items = LibCmsEdit_3.get_category_data(this.items_all ,this.category_save_id)
            }
        },
        delete_items: function(){
            var s = "全てのデータを削除します。よろしいですか？バックアップが無い場合は復元できません"
            if(window.confirm(s)){
                db.cms_edit.clear()
                db.pages.clear()            
                db.category.clear()
                this.set_exStorage(this.sysConst.KEY_NEXT_ACTION , "/edit" )
                window.location.href = this.sysConst.HTTP_URL                
            }
        },                                
    }
}
</script>
