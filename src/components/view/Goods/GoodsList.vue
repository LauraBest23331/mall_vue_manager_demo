<template>
  <div @click="$emit('makeCla')" class="container">
    <el-breadcrumb separator-class="el-icon-arrow-right" class="mb-5">
    <el-breadcrumb-item>首页</el-breadcrumb-item>
    <el-breadcrumb-item>商品管理</el-breadcrumb-item>
    </el-breadcrumb>   

    <el-row>
        <el-col :span="6" :offset="15">
          <el-button icon="el-icon-plus" @click="allFormVisible = true" type="primary">
          添加商品</el-button></el-col>
        
    </el-row>
    <div class="mt-10">


      <el-table   class="text-center"  :data="dataList"  style="width: 100%">
          <el-table-column min-width="120px" prop="gname" label="商品名">
          </el-table-column>
          
          <el-table-column min-width="120px"  prop="price" label="价格">
          </el-table-column>

          <el-table-column label="操作">
            <template slot-scope="scope">
              <el-button 
                size="mini"
                @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
              <el-button
                size="mini"
                type="danger"
                @click="handleDelete(scope.$index, scope.row)">删除</el-button>
            </template>
          </el-table-column>
      </el-table>

    </div>
<el-dialog
  title="提示"
  :visible.sync="deleteVisible"
  width="82%"
  :before-close="handleClose">
  <span>确认删除？</span>
  <span slot="footer" class="dialog-footer">
    <el-button @click="deleteVisible = false">取 消</el-button>
    <el-button type="danger" @click="deleteGoods">删 除</el-button>
  </span>
</el-dialog>
<el-dialog title="添加商品" :visible.sync="allFormVisible">
  <el-form :model="form" ref="loginFormRef" :rules="rules">
    <el-form-item  label="商品名" prop="gname" :label-width="formLabelWidth">
      <el-input v-model="form.gname"  autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item  label="商品图片" prop="pic" :label-width="formLabelWidth">
      <el-input v-model="form.pic"  autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item  label="价格" prop="price" :label-width="formLabelWidth">
      <el-input v-model="form.price"  autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item  label="描述" prop="desc" :label-width="formLabelWidth">
      <el-input v-model="form.desc"  autocomplete="off"></el-input>
    </el-form-item>

  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="allFormVisible = false">取 消</el-button>
    <el-button type="primary" @click="makeAdd()">确 定</el-button>
  </div>
</el-dialog>
<el-dialog title="修改商品" :visible.sync="editgoodsVisible">
  <el-form :model="nowGoods" ref="loginFormRef" :rules="rules">
    <el-form-item  label="商品名" prop="gname" :label-width="formLabelWidth">
      <el-input v-model="nowGoods.gname"  autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item  label="商品图片" prop="pic" :label-width="formLabelWidth">
      <el-input v-model="nowGoods.pic"  autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item  label="价格" prop="price" :label-width="formLabelWidth">
      <el-input v-model="nowGoods.price"  autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item  label="描述" prop="desc" :label-width="formLabelWidth">
      <el-input v-model="nowGoods.desc"  autocomplete="off"></el-input>
    </el-form-item>

  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="editgoodsVisible = false">取 消</el-button>
    <el-button type="primary" @click="makeEdit()">确 定</el-button>
  </div>
</el-dialog>
  </div>
</template>

<script>

export default {
  
  data(){
    return {
      tempTag: '',
      tempClan: -1,
      tempRank: -1,
      dataList: [],
      form:{
        desc:'',
        gname:'',
        price:0,
        pic:''
      },
      rules: {

      },
      nowGoods:{
        desc:'',
        gname:'',
        price:-1,
        pic:''
      },
      nowIndex:-1,
      goods:{},
      editgoodsVisible: false,
      deleteVisible: false,
      allFormVisible:false,
      formLabelWidth: '80px'
    }
  },
  components:{
  },
  created: async function(){
    const that = this
  
    that.logingoods = JSON.parse(localStorage.getItem('logingoods'))
    const {
        data: res
    } = await that.$http.get('/goods')
    if (res.code == 200) {
      this.dataList = res.data.reverse()
      console.log(this.dataList);
    }

   

  },

  methods: {
    makeAdd(){
      const that = this
        this.$refs.loginFormRef.validate(async valid=>{
            if (!valid) return
            
            that.allFormVisible = false
            console.log(that.form);
            this.$http.post('/goods', {
              ...that.form
            }).then(res=>{
              if (res.data.code == 200) {
                that.$message.success('添加成功')
                that.reload()
                
              }else {
                that.$message.error('添加失败')
              }

              
            })
            
      })
    },
    async reload(){
      const that = this
  
      that.logingoods = JSON.parse(localStorage.getItem('logingoods'))
      const {
          data: res
      } = await that.$http.get('/goods')

      if (res.code == 200) {
        console.log(res.data);
        this.dataList = res.data.reverse()
      }
    },

    handleEdit(index, row) {
      console.log(index, row);
      this.editgoodsVisible = true
      this.nowGoods = row
      
    },
    makeEdit() {
      const that = this
      that.editgoodsVisible = false
      that.$http.put('/goods/',{
        ...this.nowGoods
      }).then(res=>{
        console.log(res);
        if (res.data.code == 200) {
          that.$message.success('修改成功')
          
        }else {
          that.$message.error('修改失败')

        }
      })
    },
    handleDelete(index, row) {
        this.deleteVisible = true
        this.nowGoods = row
        this.nowIndex = index

    },
      handleClose(done) {
  
          done();

    },

    deleteGoods() {
      const that = this
      that.deleteVisible = false
      let list = this.dataList
      console.log(that.nowGoods);
      that.$http.delete("/goods/"+ that.nowGoods.id).then(res=>{
        console.log(res);
        if(res.data.code == 200) {
          that.$message.success('删除成功')
          
          list = list.splice(that.nowIndex, 1)
        }else {
          that.$message.error('删除失败')
        }
      })


      }
  }
}
</script>

<style lang="less" src="./goods_list.less">


</style>