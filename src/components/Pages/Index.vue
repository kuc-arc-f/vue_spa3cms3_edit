<template>
<div class="task_index_wrap">
    <div class="container mb-5">
        <FlashMessage></FlashMessage>
        <div class="row" style="margin-top: 10px;">
            <div class="col-sm-4"><h3>Pages</h3>
            </div>
            <div class="col-sm-4">
                <router-link :to="'/edit/pages/new'" class="btn btn-primary">Create
                </router-link>
            </div>
            <div class="col-sm-4" style="text-align: right;">
                &nbsp;&nbsp;
            </div>
        </div>
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
                <tr v-for="item in page_items" v-bind:key="item.id">
                    <td>
                        <h3>
                        <router-link :to="'/edit/pages/show/' + item.id">{{ item.title }}
                        </router-link>
                        </h3>                        
                        ID : {{ item.id }}
                        , {{ item.created_at }}
                    </td>
                    <td>
                        <router-link :to="'/edit/pages/edit/' + item.id"
                            class="btn btn-outline-primary btn-sm">Edit
                        </router-link>                         
                    </td>
                </tr>
            </tbody>
        </table>
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
    components: { FlashMessage  },
    created () {
        var config = LibCmsEdit_3.get_const()
        db = new Dexie( config.DB_NAME );
        db.version( config.DB_VERSION).stores( config.DB_STORE ); 

        this.get_items()
    },
    data () {
        return {
            page_items: [],
        }
    },
    methods: {
        get_items(){
            var self = this
            db.pages.toArray().then(function (items ) {
                self.page_items = LibDexie.get_reverse_items(items)
// console.log( items )
            });
        },
                
    }
}
</script>
