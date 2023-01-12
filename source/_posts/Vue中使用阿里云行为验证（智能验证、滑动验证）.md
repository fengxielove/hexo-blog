---
title: Vue中使用阿里云行为验证（智能验证、滑动验证）
excerpt: Vue中如何集成阿里云的智能验证与滑动验证呢？
date: 2021-05-26 16:45:40
tags: [vue.js]
---

## 首先在阿里云控制台中新增配置

![image](https://z3.ax1x.com/2021/05/26/29KLJx.png)

## Vue中使用滑动验证

### 首先在 public/index.html 中引入 script 标签

```javascript

<script type="text/javascript" charset="utf-8" src="//g.alicdn.com/sd/ncpc/nc.js?t=2015052012"></script>

```

### 新建SlideCaptcha组件
```vue
<template>
  <div>
    <div id="slideCaptcha" class="nc-container"></div>
  </div>
</template><script>
export default {
  name: 'testVPatch',
  data() {
    return {
      appkey: '你自己的appkey',
      scene: 'ic_login',
      nc: null
    }
  },
  methods: {
    init() {
      const nc_token = ["(这里随意填)", (new Date()).getTime(), Math.random()].join(':');
      const NC_Opt = {
        renderTo: "#slideCaptcha",
        appkey: "你自己的appkey",
        scene: "nc_message",
        token: nc_token,
        customWidth: 300,
        trans: {"key1": "code0"},
        elementID: ["usernameID"],
        is_Opt: 0,
        language: "cn",
        isEnabled: true,
        timeout: 3000,
        times: 5,
        // 用于自定义滑动验证各项请求的接口地址。一般情况，请勿配置该参数。
        apimap: {},
        callback: function (data) {
          window.console && console.log(nc_token)
          window.console && console.log(data.csessionid)
          window.console && console.log(data.sig)
        }
      }
      this.nc = new noCaptcha(NC_Opt)
      this.nc.upLang('cn', {
        _startTEXT: "请按住滑块，拖动到最右边",
        _yesTEXT: "验证通过",
        _error300: "哎呀，出错了，点击<a href=\"javascript:__nc.reset()\">刷新</a>再来一次",
        _errorNetwork: "网络不给力，请<a href=\"javascript:__nc.reset()\">点击刷新</a>",
      })
    },
    reloadSlideCap() {
      this.nc.reload()
    }
  },
  mounted() {
    this.init()
  }
};
</script>
```
## Vue中使用智能验证
### 注意点：
- 全局变量window.NVC_Opt的声明必须在引入//g.alicdn.com/sd/nvc/1.1.112/guide.js资源前配置完成。
- new smartCaptcha的声明必须在引入//g.alicdn.com/sd/nvc/1.1.112/guide.js资源后配置。
### 同样的，先在public/index.html 中引入 script 标签
```javascript
    <script src="//g.alicdn.com/sd/smartCaptcha/0.0.4/index.js"></script>
    <script src="//g.alicdn.com/sd/quizCaptcha/0.0.1/index.js"></script>
```
### 然后新建组件
```vue
<template>
<div>
  <Verify @loaded="loaded"></Verify>
  <div id="captcha"></div>
</div>
</template>

<script>
// import Verify from "./Verify";
export default {
  name: "AiCaptcha",
  components: {
    Verify: {
      render(createElement) {
        const self = this;
        return createElement("script", {
          attrs: {
            type: "text/javascript",
            src: "//g.alicdn.com/sd/nvc/1.1.112/guide.js"
          },
          on: {
            load() {
              self.$emit("loaded")
            }
          }
        })
      }
    }
  },
  data() {
    return {
      ic: null
    }
  },
  methods: {
    loaded() {
      console.log("智能验证加载成功")
    },
    initCaptcha() {
      this.ic = new smartCaptcha({
        appkey:'你自己的appkey',
        scene:'ic_login',
        renderTo: '#captcha',
        width: 300,
        height: 42,
        default_txt: '点击按钮开始智能验证',
        success_txt: '验证成功',
        fail_txt: '验证失败，请在此点击按钮刷新',
        scaning_txt: '智能检测中',
        success: function(data) {
          console.log(data)
        },
        fail: function(data) {
          console.log('ic error', data);
        }
      })
      this.ic.init()
    }
  },
  mounted() {
    this.initCaptcha()
  },
  created() {
    window.NVC_Opt = {
      appkey:'你自己的appkey',
      scene:'ic_login',
      renderTo:'#captcha',
      trans: {"key1": "code0", "nvcCode":400}
    }
  }
}
</script>

<style scoped>

</style>

```
## 总结
在使用智能验证时，需要先在组件内使用 render 函数渲染一个script标签出来，然后根据父子组件的生命周期，让资源的加载满足注意点中的要求。

[Vue中使用阿里云行为验证（智能验证、滑动验证）](https://www.jianshu.com/p/13b09c44b78f)
