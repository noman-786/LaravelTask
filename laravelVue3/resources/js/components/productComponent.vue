<template>
    <div>
        <!-- Button trigger modal -->
        <button type="button" class="btn btn-primary mt-5" data-bs-toggle="modal" data-bs-target="#exampleModal">
        Add product
        </button>
        <!-- Modal -->
        <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title" id="exampleModalLabel">Product Details</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
                <div class="form-group">
                    <label>Product Title</label>
                    <input type="text" class="form-control" id="productTitle" name="productTitle" placeholder="Enter product title" v-model="item.productTitle"/>
                </div>
                <div class="form-group">
                    <label>Product Description</label>
                    <textarea class="form-control" id="productDesc" name="productDesc" placeholder="Enter product description" cols="15" rows="4" v-model="item.productDesc"></textarea>
                </div>
                <div class="form-group">
                    <label>Product Price</label>
                    <input type="number" class="form-control" id="productPrice" name="productPrice" placeholder="Enter product price" v-model="item.productPrice"/>
                </div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-outline-info btn-block" @click="saveData" data-bs-dismiss="modal" aria-label="Close">
                    {{isEdit ? 'Update Product':'Save Product'}}
                </button>
            </div>
            </div>
        </div>
        </div>

        <!-- list of products -->
        <div class="container mt-3" v-if="lists.length > 0">
            <h1 class="text-center my-2">List of Products</h1>
            <table class="table table-hover table-striped">
                <thead>
                    <tr>
                        <th>Product Id</th>
                        <th>Product Title</th>
                        <th>Product Description</th>
                        <th>Product Price</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="product in lists" :key="product.id">
                        <td>{{product.id}}</td>
                        <td>{{product.productTitle}}</td>
                        <td>{{product.productDesc}}</td>
                        <td>{{product.productPrice}}</td>
                        <td>
                            <button class="btn btn-outline-success" data-bs-toggle="modal" data-bs-target="#exampleModal" data-bs-dismiss="modal" aria-label="Close" @click="editData(product)">Edit</button>
                            <button class="btn btn-outline-danger" @click="delData(product.id)">Delete</button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
        <!-- list of products -->

    </div>
</template>
<script>
import $ from 'jquery'
export default {
    name:'product',
    data(){
        return {
            lists:[],
            item:{
                productTitle:"",
                productDesc:"",
                productPrice:""
            },
            tempId:null,
            isEdit:false
        }
    },
    mounted(){
        this.fetchData();
    },
    methods:{
        fetchData(){
            axios.get('/api/product').then(res=>this.lists=res.data);
        },
        saveData(){
            let method=axios.post;
            let url='/api/product';
            if(this.isEdit){
                method=axios.put;
                url=`/api/product/${this.tempId}`;
            }
            try{
                method(url,this.item).then(res=>{
                    this.fetchData();
                    this.item={
                        productTitle:"",
                        productDesc:"",
                        productPrice:""
                        };
                    this.tempId=null;
                    this.isEdit=false;
                });
            }
            catch(e){
                console.log(e);
            }
            $("#exampleModal").modal('hide');
        },
        editData(pro){
            this.item={
                productTitle:pro.productTitle,
                productDesc:pro.productDesc,
                productPrice:pro.productPrice
            }
            this.tempId=pro.id;
            this.isEdit=true;
        },
        delData(id){
            try{
                axios.delete(`/api/product/${id}`).then(res=>this.fetchData());
            }
            catch(e){

            }
        }
    }
}
</script>

<style>

</style>
