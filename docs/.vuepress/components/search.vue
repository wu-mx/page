<template>
  <div id="app">
    <div class="card">
      <div class="card-body">
        <h1 class="card-title">数据泄露查询</h1>
        <div v-if="status.form">
          <div class="row">
            <div class="col">
              <input type="text" v-model="datas.name" class="form-control" onkeyup = "this.value=this.value.replace(/[^\u4e00-\u9fa5]/g,'')" maxlength="5" placeholder="姓名">
            </div>
          </div>
          <div class="row">
            <div class="col">
              <input type="text" v-model="datas.idFirst" class="form-control" onkeyup = "value=value.replace(/[^\d]/g,'')" maxlength="1" placeholder="身份证第一位">
            </div>
            <div class="col">
              <input type="text" v-model="datas.idLast" class="form-control" onkeyup = "value=value.replace(/[^\d]/g,'')" maxlength="1" placeholder="身份证最后一位">
            </div>
          </div>
          <div class="row">
            <div class="col">
              <button @click="submit()" type="submit" class="btn btn-primary">查询</button>
            </div>
        </div>
        </div>
        <div v-if="status.error">
          <div class="alert alert-danger" role="alert">
            <strong>错误！</strong> {{status.error}}
          </div>
      </div>
        <div v-if="status.success && status.result.length > 0">
          <div class="alert alert-success" role="alert">
            <strong>成功！</strong> 您的个人信息已泄露，请及时联系相关部门。
          </div>
    </div>
  </div>
</div>
  </div>
</template>


<script>
import './bootstrap.bundle.min.js'
export default {
  name: "search",
  data(){
    return{
      status:{form:true},
      datas:{name:'',idFirst:'',idLast:''},
      result:[]
    }
  },
  methods:{
    submit(){
      if(datas.name !== undefined && datas.idFirst !== undefined && datas.idLast !== undefined){
        const Http = new XMLHttpRequest();
        const url=`https://shang-hai-search.vercel.app/api/search?name=${datas.name}&idFirst=${datas.idFirst}&idLast=${datas.idLast}`;
        Http.open("GET", url);
        Http.send();

        Http.onreadystatechange = (e) => {
          console.log(Http.responseText)
          if(Http.readyState === 4 && Http.status === 304){
            this.status.form = false
            let res = JSON.parse(Http.responseText)
            if(res.code === '0'){
                this.result = res.data
            }else{
                this.status.error = true
                this.status.errorMsg = res.message
            }
          }else{
            this.status.form = false;
            this.status.error = true;
            this.status.errorMsg = '网络错误'
          }
        }
      }else{
        alert("请填写信息")
      }
    }
  }
}
</script>
<style src="./bootstrap.min.css">
</style>