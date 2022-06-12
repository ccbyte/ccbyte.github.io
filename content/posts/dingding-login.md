---
title: "H5实现钉钉扫码登录"
date: 2022-02-20T19:33:03
tags: ["javascript"]
---
# 总体思路
官方有两种实现方式
1. 使用钉钉提供的扫码登录页面
2. 支持网站将钉钉登录二维码内嵌到自己页面中

这里我们采用效果比较好的第二种

配置使用sdk -> DDLogin生成二维码 -> 扫码得到返回的code -> 用code访问一个会302的钉钉地址（iframe -> postMessage -> 来源校验）

# 实现细节

## 配置回调域名
进入钉钉开发者后台
1. 进入已创建的应用详情页，在基础信息页面可以查看到应用的SuiteKey/SuiteSecret(第三方企业应用)或AppKey/AppSecret(企业内部应用)。

2. 在应用详情页，然后单击钉钉登录与分享，添加应用回调的URL，以http或https开头。


## 添加sdk
在index.html中加入钉钉的Api
```html
<script src="https://g.alicdn.com/dingding/dinglogin/0.0.5/ddLogin.js"></script>
```

## 添加wait页面
```html
<template>
    <div></div>
</template>

<script>
export default {
    data(){
        return{
            
        }
    },
    mounted(){
        this.loadLogin()
    },
    methods:{
        async loadLogin(){
            const loading = this.$loading({
                lock: true,
                text: '登陆中',
                spinner: 'el-icon-loading',
                background: 'rgba(0, 0, 0, 0.7)'
            });
            const { code } = this.$route.query
            if(code){
                const data = await this.$store.dispatch('admin/login', {
                    code
                })               
                loading.close();
                if (!data.user_role.length) {
                    this.$message({
                        message: '登录失败，无法获取到用户身份',
                        type: 'warning'
                    })
                    return
                }
                this.$message({
                    message: '登录成功',
                    type: 'success'
                })
                this.$router.push({ name: 'serverManage' })
            }
        }
    }
}
</script>
```
在route中添加

在白名单中添加

## 添加扫码入口
在页面添加二维码div+js
```html
<div id="qr-code" ref="qrCode" class="qr-code" v-show="isQrCode"></div>
```
```js
selectQR() {//   选择钉钉二维码登录
    this.isQrCode = !this.isQrCode
    this.$refs.qrCode.innerHTML = ''

     // 切换登录模式图标
    if(this.isQrCode){
        this.QRurl = 'url('+computerImg+')' // PC登录
    }else{
        this.QRurl = 'url('+qrImg+')' // 扫码登录
    }          
    this.$el.style.setProperty('--QRurl',this.QRurl)

    // 参数初始化
    let url = encodeURIComponent(location.origin + location.pathname +'#/wait')
    let goto = encodeURIComponent('https://oapi.dingtalk.com/connect/qrconnect?appid=${SuiteKey}&response_type=code&scope=snsapi_login&state=STATE&redirect_uri='+url)
    // suiteKey示例ding2333qwer233asd233

    // 调用登录API
    var obj = DDLogin({
        id:"qr-code",//这里需要你在自己的页面定义一个HTML标签并设置id，例如<div id="login_container"></div>或<span id="login_container"></span>
        goto: goto, //请参考注释里的方式
        style: "border:none;background-color:#FFFFFF;",
        width : "300",
        height: "300"
    })
},
```

```css
.qr-code{
    background: var(--QRurl);
    height: 50px;
    width: 50px;
    position: absolute;
    left: 0;
    top: 0;
    display: inline-block;
}
```

## 登录校验
> 引入的js会在获取用户扫描之后将获取的`loginTmpCode`通过`window.parent.postMessage(loginTmpCode,'*');`返回给您的网站。
```js
// vue写在mounted钩子中
this.$el.style.setProperty('--QRurl','url('+qrImg+')')
if (typeof window.addEventListener != 'undefined') {
    window.addEventListener('message',this.handleMessage, false)
} else if (typeof window.attachEvent != 'undefined') {
    window.attachEvent('onmessage', this.handleMessage);
}
```
```js
handleMessage(event) {
    let origin = event.origin;            
    if( origin == "https://login.dingtalk.com" ) { //判断是否来自ddLogin扫码事件。
        let loginTmpCode = event.data;
        
        // 拿到loginTmpCode跳转
        window.location.href = 'https://oapi.dingtalk.com/connect/oauth2/sns_authorize?appid=${SuiteKey}&response_type=code&scope=snsapi_login&state=STATE&redirect_uri=REDIRECT_URI&loginTmpCode='+loginTmpCode
        // 链接处理成功会返回302跳转到goto参数指定的redirect_uri，并向url参数中追加临时授权码code及state参数
    }
}
```

# 其它
[官方文档](https://open.dingtalk.com/document/orgapp-server/scan-qr-code-to-log-on-to-third-party-websites)

正式环境和测试环境appid可能不同，可以通过process.env或location.host区分

企业内部应用/第三方企业应用通过unionId判断是否为公司员工

