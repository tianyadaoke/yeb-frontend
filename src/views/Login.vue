<template>
    <div>
       <el-form :rules="rules" ref="loginForm" :model="loginForm" class="loginContainer"
        v-loading="loading"
        element-loading-text="正在登陆"
        element-loading-spinner="el-icon-loading"
        element-loading-background="rgba(0, 0, 0, 0.8)">
           <h3 class="loginTitle">系统登陆</h3>
           <el-form-item prop="username">
               <el-input v-model="loginForm.username" autocomplete="false"  placeholder="请输入用户名" type="text"></el-input>
           </el-form-item>
           <el-form-item prop="password">
               <el-input v-model="loginForm.password" autocomplete="false"  placeholder="请输入密码" type="password"></el-input>
           </el-form-item>
           <el-form-item prop="code">
               <el-input v-model="loginForm.code" autocomplete="false"  placeholder="点击图片更换验证码" type="text"
               style="width:250px;margin-right:5px"></el-input>
                <img :src="captchaUrl" @click="updateCaptcha">
           </el-form-item>    
           <el-checkbox v-model="checked" class="loginRemember">记住我</el-checkbox>
           <el-button type="primary" style="width:100%" @click="submitLogin">登陆</el-button>
       </el-form>

    </div>
</template>

<script>

export default {
    name: "Login",
    data(){
        return{
            captchaUrl:'/captcha?time='+new Date(),
            checked:true,
            loading:false,
            loginForm:{
                username:'admin',
                password:'123',
                code:''
            },
            rules:{
                username:[{required:true,message:'请输入用户名',trigger:'blur'}],
                password:[{required:true,message:'请输入密码',trigger:'blur'}],
                code:[{required:true,message:'请输入验证码',trigger:'blur'}]
            }
        }
    },
    methods:{
        submitLogin(){
            this.$refs.loginForm.validate((valid)=>{
                if(valid){
                    this.loading=true;
                    console.log(this.loginForm);
                    this.postRequest('/login',this.loginForm).then(resp=>{           
                        if(resp){
                            this.loading=false;
                            const tokenStr=resp.obj.tokenHead+resp.obj.token;
                            window.sessionStorage.setItem('tokenStr',tokenStr);
                            this.$router.replace('/home')
                        } else{
                            this.loading=false;
                            console.log("login error");
                        }
                    })
                }else{
                   this.$message.error('请输入所有字段');
                    return false;
                }
            })
        },
        updateCaptcha(){
            this.captchaUrl='/captcha?time='+new Date();
        }
    }
}
</script>
<style>
    .loginContainer{
        border-radius: 15px;
        background-clip: padding-box;
        margin: 180px auto; 
        width: 350px;
        padding: 15px 35px 15px 35px;
        background: #fff;
        border: 1px solid #eaeaea;
        box-shadow: 0 0 25px #cac6c6;
        
    }
    .loginTitle{
        margin: 0px auto 40px auto;
        text-align: center;
    }
    .loginRemember{
        text-align: center;
        margin: 0px 0px 15px 0px;
    }
    .el-form-item__content{
        display: flex;
        align-items: center;
    }
</style>