<template>
  <div class="container" v-loading="loading">
    <el-form ref="form" :model="form" label-width="100px" label-position="left">
      <el-form-item label="收取项名称">
        <el-input v-model="form.name" @blur="setDefaultNameRule"></el-input>
      </el-form-item>
      <el-form-item label="命名规则">
        <ul style="list-style: none;padding: 0;margin: 0">
          <li v-for="(r, index) in nameRules" :key="index" style="margin-bottom: 10px">
            <el-radio v-model="rulesRadio" :label="index" border style="">
              {{r.title}}：
              <el-tag v-for="(t, index) in r.rule"
                      v-bind:key="index"
                      :type="t.changeAble ? '' : 'success'"
                      style="margin-left: 5px;margin-bottom: 15px"
              >{{t.value === '' ? '"收取项名称"待输入' : t.value}}</el-tag>
            </el-radio>
          </li>
        </ul>
        <el-button type="text" @click="dialogVisible = true">+ 自定义</el-button>
      </el-form-item>
      <el-form-item label="截止日期">
        <el-date-picker
            v-model="ddl"
            type="datetime"
            placeholder="选择日期时间"
            value-format="yyyy-MM-dd HH:mm">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="共计人数">
        <el-input v-model="form.number" style="width: 200px"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">立即创建</el-button>
        <el-button>帮助</el-button>
      </el-form-item>
    </el-form>

    <el-dialog
        title="自定义命名规则"
        :visible.sync="dialogVisible"
        width="1070px">
      <create-rule :addRule="addRule"/>
      <span slot="footer" class="dialog-footer">
  </span>
    </el-dialog>
  </div>
</template>

<script>
import CreateRule from "@/components/CreateRule";
export default {
  name: "CreateCollect",
  components:{CreateRule},
  data() {
    return {
      loading: false,
      rulesRadio:0,
      dateTimerRange:'',
      ddl:'',
      nameRules:[
        {
          title:'默认规则',
          rule:[
            {
              changeAble:true,
              value:'学号'
            },
            {
              changeAble: false,
              value: '-'
            },
            {
              changeAble: true,
              value: '姓名'
            },
            {
              changeAble: false,
              value: '-'
            },
            {
              changeAble: false,
              value: ''
            }
          ]
        },
        ],
      dialogVisible: false,
      form: {
        name: '',
        number:'',
      }
    }
  },
  computed:{

  },
  methods:{
    test(){
      console.log(this.ddl)
    },
    onSubmit() {
      //console.log('submit!');
      this.loading = true
      this.$axios({
        method: "post",
        url: "/file-receive/create",
        responseType: "json",
        data:{
          ddl: this.ddl,
          jsonNameRules: this.nameRules[this.rulesRadio],
          num: this.form.number,
          zipName: this.form.name
        }
      }).then(
          (res) => {
            if (res.status === 200) {
              this.loading = false
              this.$message.success("创建成功")
              this.$router.push({name:'CollectList'})
            }
          },
          (err) => {
            this.loading = false
            this.$message.error("创建失败" + err)
          }
      );
    },
    setDefaultNameRule(){
      this.nameRules[0].rule[4].value = this.form.name
    },
    addRule(rule){
      //console.log(rule)
      let r = JSON.parse(rule)
      this.nameRules.push(r)
      this.dialogVisible = false
      this.rulesRadio = this.nameRules.length - 1
    }
  }
}
</script>

<style scoped>
  .container{
    width: 700px;
    margin-left: 50px;
  }
  .el-radio.is-bordered {
    padding: 9px 10px 5px 10px;
    height: 50px;
  }
</style>