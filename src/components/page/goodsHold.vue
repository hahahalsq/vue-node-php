<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-cascades"></i> 房屋持有列表
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">

            <el-table
                :data="tableData"
                border
                class="table"
                ref="multipleTable"
                header-cell-class-name="table-header"
                @selection-change=""
            >
                <el-table-column width="280" label="图片" align="center">
                    <template slot-scope="scope">
                        <div style="text-align:center;">
                        <img :src="require('../../assets/upload/'+scope.row.firstImg)" style="width:200px;height:180px;" />
                        </div>
                    </template>
                </el-table-column>
                <!-- <el-table-column type="selection" width="55" align="center"></el-table-column> -->
                <el-table-column prop="location" label="位置" align="center"></el-table-column>
                <el-table-column prop="price" label="租金(￥)" align="center" sortable></el-table-column>
                <el-table-column prop="amount" label="面积(m²)" align="center" sortable></el-table-column>
                <el-table-column label="房型" align="center">
                    <template slot-scope="scope">
                        <div v-show="scope.row.type == 'aaa'"
                        >一室一厅</div>
                        <div v-show="scope.row.type == 'bbb'"
                        >两室一厅</div>
                        <div v-show="scope.row.type == 'ccc'"
                        >三室一厅</div>
                        <div v-show="scope.row.type == 'ddd'"
                        >三室两厅</div>
                    </template>
                </el-table-column>
                <el-table-column label="详情">
                    <template slot-scope="scope">
                        {{scope.row.content}}
                    </template>
                </el-table-column>
                <el-table-column label="状态" align="center">
                    <template slot-scope="scope">
                        <el-tag v-show="scope.row.state == 1" type='success'
                        >在售</el-tag>
                        <el-tag v-show="scope.row.state != 1" type='danger'
                        >已下架</el-tag>
                    </template>
                </el-table-column>

                <el-table-column label="操作" width="180" align="center">
                    <template slot-scope="scope">
                        <el-button
                            type="text"
                            icon="el-icon-edit"
                            @click="handleEdit(scope.$index, scope.row)" v-show="scope.row.state != 1" 
                        >上架</el-button>
                        <el-button
                            type="text"
                            icon="el-icon-edit"
                            @click="handleEdit(scope.$index, scope.row)" v-show="scope.row.state == 1 "
                        >下架</el-button>
                    </template>
                </el-table-column>

            </el-table>

        </div>

        <!-- 编辑弹出框 -->
<!--         <el-dialog title="编辑" :visible.sync="editVisible" width="30%">
            <el-form ref="form" :model="form" label-width="70px">
                <el-form-item label="用户名">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="地址">
                    <el-input v-model="form.address"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editVisible = false">取 消</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </span>
        </el-dialog> -->




    </div>
</template>

<script>
import { fetchData } from '../../api/index';
import axios from 'axios'
export default {
    name: 'basetable',
    data() {
        return {
            query: {
                address: '',
                name: '',
                pageIndex: 1,
                pageSize: 10
            },
            tableData: [],
            multipleSelection: [],
            delList: [],
            editVisible: false,
            pageTotal: 0,
            form: {},
            idx: -1,
            id: -1,
            username:'',

            returnImgUrlAll:[], // 整合全部路径

        };
    },
    created() {
        this.getHoldData();
    },
    mounted(){
        console.log('~~~~~')
    },

    watch: {
      // 如果路由发生变化，再次执行该方法
      "$route": "getHoldData"
    },

    // beforeRouteEnter(to, from, next) {
    //     const that = this
    //     var name = localStorage.getItem('ms_username')
    //     axios.post('http://127.0.0.1:3000/getHoldList', {
    //         username:name
    //     }).then(function(response) {
    //             //成功时服务器返回 response 数据
    //             console.log('~~~~~~~~~~~')
    //             console.log(response)
    //             if(response.data.length){
    //                 that.tableData = response.data
    //             }else{
    //                 that.$message.error('没有数据')
    //                 return false
    //             }
    //         })
    //         .catch(function(error) {
    //             console.log(error);
    //         });
    // },
    methods: {
        getHoldData() {
            //向服务器提交数据
            const that = this
            that.username = localStorage.getItem('ms_username')
            var name = that.username
            axios.post('http://127.0.0.1:3000/getHoldList', {
                username:name
            }).then(function(response) {
                    //成功时服务器返回 response 数据
                    if(response.data.length){
                        that.tableData = response.data
                        for(var i=0;i<that.tableData.length;i++){
                            var temparr = that.tableData[i].imgs
                            var firstImg = temparr.split('+')[1]
                            that.tableData[i].firstImg = firstImg
                        }
                        console.log(that.tableData)
                    }else{
                        that.$message.error('没有数据')
                        return false
                    }
                })
                .catch(function(error) {
                    console.log(error);
                });
        },
        // 触发搜索按钮
        // handleSearch() {
        //     this.$set(this.query, 'pageIndex', 1);
        //     this.getData();
        // },
        // // 删除操作
        // handleDelete(index, row) {
        //     // 二次确认删除
        //     this.$confirm('确定要删除吗？', '提示', {
        //         type: 'warning'
        //     })
        //         .then(() => {
        //             this.$message.success('删除成功');
        //             this.tableData.splice(index, 1);
        //         })
        //         .catch(() => {});
        // },
        // // 多选操作
        // handleSelectionChange(val) {
        //     this.multipleSelection = val;
        // },
        // delAllSelection() {
        //     const length = this.multipleSelection.length;
        //     let str = '';
        //     this.delList = this.delList.concat(this.multipleSelection);
        //     for (let i = 0; i < length; i++) {
        //         str += this.multipleSelection[i].name + ' ';
        //     }
        //     this.$message.error(`删除了${str}`);
        //     this.multipleSelection = [];
        // },
       // 编辑操作
        handleEdit(index, row) {
            var location = row.location
            var id = row.id
            var stateChange = row.state == 1 ? 0 : 1
            const that = this
            var username = localStorage.getItem('ms_username')
            axios.post('http://127.0.0.1:3000/goodsChangeState', {
                state:stateChange,
                id:id,
            }).then(function(response) {
                    if(response.status == 200)
                    {
                        that.getHoldData();
                    }else{
                        that.$message.error('没有数据')
                        return false
                    }
                })
                .catch(function(error) {
                    console.log(error);
                });  

        },
        // 保存编辑
        // saveEdit() {
        //     this.editVisible = false;
        //     this.$message.success(`修改第 ${this.idx + 1} 行成功`);
        //     this.$set(this.tableData, this.idx, this.form);
        // },
        // // 分页导航
        // handlePageChange(val) {
        //     this.$set(this.query, 'pageIndex', val);
        //     this.getData();
        // }
    }
};
</script>

<style scoped>
.handle-box {
    margin-bottom: 20px;
}

.handle-select {
    width: 120px;
}

.handle-input {
    width: 300px;
    display: inline-block;
}
.table {
    width: 100%;
    font-size: 14px;
}
.red {
    color: #ff0000;
}
.mr10 {
    margin-right: 10px;
}
.table-td-thumb {
    display: block;
    margin: auto;
    width: 40px;
    height: 40px;
}
</style>
