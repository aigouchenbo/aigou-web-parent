<template>
    <el-container style="height: 630px">
        <el-aside width="300px">
            <!--@node-click="handleNodeClick"-->
            <!--default-expand-all :expand-on-click-node="false"-->
            <el-tree :data="productTypes" :props="defaultProps" node-key="id"  @node-contextmenu = "rihgtClick"></el-tree>
        </el-aside>
        <!--<span class="custom-tree-node" slot-scope="{ node, data }">-->
              <!--<span>-->
                 <!--<i v-if ="node.data.flag" :class="node.icon[0]"> </i>-->
                 <!--<i v-else-if="node.data.flag === false" :class = "node.icon[1]"></i>-->
                   <!--{{ node.label }}-->
              <!--</span>-->
                <!--<span>-->
              <!--&lt;!&ndash;定义菜单及菜单项的操作&ndash;&gt;-->
                   <!--<div v-show = "menuVisible" ref="menu">-->
                     <!--<ul id = "menu">-->
                       <!--<li tabindex="-1" class="menu__item" id="menu-4994-1-0" @click = "() => append(data)">-->
                         <!--<i class="el-icon-circle-plus-outline"></i>-->
                           <!--添加-->
                         <!--</li>-->
                         <!--<li tabindex="-1" class="menu__item" id="menu-4994-1-1">-->
                           <!--<i class="el-icon-edit"></i>-->
                           <!--修改-->
                        <!--</li>-->
                        <!--<li tabindex="-1" class="menu__item" id="menu-4994-1-2"  @click = "() => remove(data)">-->
                           <!--<i class="el-icon-remove-outline"></i>-->
                           <!--删除-->
                        <!--</li>-->
                      <!--</ul>-->
                    <!--</div>-->
                <!--</span>-->
         <!--</span>-->
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
            append(data) {
                console.log("当前data为：",data);
                const newChild = { id: id++, label: 'testtest', children: [] };
                if (!data.children) {
                    this.$set(data, 'children', []);
                }
                data.children.push(newChild);
            },
            remove(node, data) {
                const parent = node.parent;
                const children = parent.data.children || parent.data;
                const index = children.findIndex(d => d.id === data.id);
                children.splice(index, 1);
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