<template>
<div class="task_index_wrap">
    <div class="container">
        <FlashMessage></FlashMessage>
        <div class="row" style="margin-top: 10px;">
            <div class="col-sm-4"><h3>CmsCategory</h3>
            </div>
            <div class="col-sm-4">
                <router-link :to="'/cms_category/new/'" class="btn btn-primary">Create
                </router-link>
            </div>
            <div class="col-sm-4" style="text-align: right;">
                &nbsp;&nbsp;
            </div>
        </div>
        <table class="table table-hover mt-2 mb-5">
            <thead>
            <tr>
                <th>Title</th>
                <th>
                    Actions
                </th>
            </tr>
            </thead> 
            <tbody>
                <tr v-for="item in category_items" v-bind:key="item.id">
                    <td>
                        <h3>
                        <router-link :to="'/cms_category/edit/' + item.id">{{ item.name }}</router-link>
                        </h3>                        
                        ID : {{ item.id }}
                        , {{ item.created_at }}
                    </td>
                    <td>
                        <router-link :to="'/cms_category/edit/' + item.id"
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
//import navbar from '@/components/Layouts/Navbar'
import Dexie from 'dexie';
import LibDexie from '@/libs/LibDexie';
import LibCmsEdit_3 from '@/libs/LibCmsEdit_3';

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
            category_items : [],
        }
    },
    methods: {
        get_items(){
            var self = this
            db.category.toArray().then(function (items ) {
                self.category_items = LibDexie.get_reverse_items(items)
//                console.log( items )
            });
        },
    }
}
</script>
