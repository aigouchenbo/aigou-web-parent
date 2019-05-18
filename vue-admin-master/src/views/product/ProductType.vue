<template>
    <el-container style="height: 630px">
        <el-aside width="300px">
            <!--@node-click="handleNodeClick"-->
            <!--default-expand-all :expand-on-click-node="false"-->
            <el-tree :data="productTypes" :props="defaultProps" node-key="id"  @node-contextmenu = "rihgtClick"></el-tree>
            <div v-show = "menuVisible">
                <ul id = "menu">
                    <li tabindex="-1" class="menu__item" id="menu-4994-1-0">添加</li>
                    <li tabindex="-1" class="menu__item" id="menu-4994-1-1">修改</li>
                    <li tabindex="-1" class="menu__item" id="menu-4994-1-2">删除</li>
                </ul>
            </div>
        </el-aside>
        <el-main>

        </el-main>
    </el-container>
</template>

<script>
    export default {
        data(){
            return {
                productTypes:[],
                defaultProps: {
                    children: 'children',
                    label: 'name'
                },
                menuVisible:false,
            }
        },
        methods:{
            //加载树的数据
            loadTreeData(){
                this.$http.get("/product/productType/tree").then((res)=>{
                    this.productTypes = res.data;
                })
            },
            rihgtClick(event,object,value,element){
                if(value.level == 1){
                    this.menuVisible = true;
                    let menu = document.querySelector("#menu");
                    /* 菜单定位基于鼠标点击位置 */
                    menu.style.left = event.clientX + 20 + "px" ;
                    menu.style.top = event.clientY -10 + "px";
                }
                console.log("右键被点击的event:",event);
                console.log("右键被点击的object:",object);
                console.log("右键被点击的value:",value);
                console.log("右键被点击的element:",element);

            },
        },
        mounted(){
            this.loadTreeData();
        }
    }
</script>

<style scoped>
    .el-aside{
        border:1px solid #ccc;
        border-right: none;
    }
    .el-main{
        border:1px solid #ccc;
    }
</style>