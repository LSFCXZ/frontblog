<template>
  <div class="layui-container fly-marginTop">
    <div class="fly-panel fly-panel-user"
      pad20>
      <div class="layui-tab layui-tab-brief"
        lay-filter="user">
        <ul class="layui-tab-title">
          <li class="layui-this">登入</li>
          <li>
            <router-link :to="{name:'reg'}">注册</router-link>
          </li>
        </ul>
        <div class="layui-form layui-tab-content"
          id="LAY_ucm"
          style="padding: 20px 0;">
          <validation-observer ref="observer"
            v-slot="{ validate }">
            <div class="layui-tab-item layui-show">
              <div class="layui-form layui-form-pane">
                <form>
                  <div class="layui-form-item">
                    <label for="L_email"
                      class="layui-form-label">用户名</label>
                    <validation-provider rules="required|email"
                      v-slot="{errors}"
                      name="username">
                      <div class="layui-input-inline">
                        <input type="text"
                          id="L_email"
                          name="username"
                          v-model="username"
                          placeholder="请输入用户名"
                          autocomplete="off"
                          class="layui-input">
                      </div>
                      <div class="layui-form-mid">
                        <span style="color: #c00">{{ errors[0] }}</span>
                      </div>
                    </validation-provider>
                  </div>
                  <div class="layui-form-item">
                    <label for="L_pass"
                      class="layui-form-label">密码</label>
                    <validation-provider rules="required|min:6|max:16"
                      v-slot="{errors}"
                      name="password">
                      <div class="layui-input-inline">
                        <input type="password"
                          id="L_pass"
                          name="password"
                          v-model="password"
                          placeholder="请输入密码"
                          autocomplete="off"
                          class="layui-input">
                      </div>
                      <div class="layui-form-mid">
                        <span style="color: #c00">{{ errors[0] }}</span>
                      </div>
                    </validation-provider>
                  </div>
                  <div class="layui-form-item">
                    <label for="L_vercode"
                      class="layui-form-label">验证码</label>
                    <validation-provider name="code"
                      ref="codefield"
                      rules="required|length:4"
                      v-slot="{ errors }">
                      <div class="layui-input-inline">
                        <input type="text"
                          id="L_vercode"
                          name="code"
                          v-model="code"
                          placeholder="请输入验证码"
                          autocomplete="off"
                          class="layui-input">
                      </div>
                      <div>
                        <span style="color: #c00;"
                          class="svg"
                          @click="_getcode()"
                          v-html="svg"></span>
                      </div>
                      <div>
                        <span style="color: #c00">{{ errors[0] }}</span>
                      </div>
                    </validation-provider>
                  </div>
                  <div class="layui-form-item">
                    <button class="layui-btn"
                      type="button"
                      @click="validate().then(submit)">立即登录</button>
                    <span style="padding-left:20px;">
                      <router-link :to="{name:'forget'}">忘记密码？</router-link>
                    </span>
                  </div>
                  <!-- <div class="layui-form-item fly-form-app">
                  <span>或者使用社交账号登入</span>
                  <a href=""
                    onclick="layer.msg('正在通过QQ登入', {icon:16, shade: 0.1, time:0})"
                    class="iconfont icon-qq"
                    title="QQ登入"></a>
                  <a href=""
                    onclick="layer.msg('正在通过微博登入', {icon:16, shade: 0.1, time:0})"
                    class="iconfont icon-weibo"
                    title="微博登入"></a>
                </div> -->
                </form>
              </div>
            </div>
          </validation-observer>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Code from '@/mixin/code'
import { login } from '@/api/login'
export default {
  name: 'Login',
  mixins: [Code],
  data () {
    return {
      username: '',
      password: ''
    }
  },
  methods: {
    async submit () {
      // 判断前端验证表单是否全部通过
      const isValid = await this.$refs.observer.validate()
      if (!isValid) {
        return
      }
      // 登录
      login({
        username: this.username,
        password: this.password,
        code: this.code,
        sid: this.$store.state.sid
      }).then((res) => {
        if (res.code === 200) {
        // 两种缓存数据的方法 localStorage sessionStorage
          // localStorage.setItem('userInfo', JSON.stringify(res.data))
          // 插入username属性，这里不是后台查询过来,因为登录成果后台不返回username
          res.data.username = this.username
          this.$store.commit('setUserInfo', res.data)
          this.$store.commit('setToken', res.token)
          this.$store.commit('setIsLogin', true)
          this.username = ''
          this.password = ''
          this.code = ''
          // 清空错误的校验信息
          requestAnimationFrame(() => {
            this.$refs.observer && this.$refs.observer.reset()
          })
          this.$router.push('/')
        } else if (res.code === 404) {
          this.$alert('用户名密码校验失败，请检查')
        } else if (res.code === 401) {
          this.$refs.codefield.setErrors([res.msg])
        }
      }).catch((err) => {
        const data = err.response.data
        // console.log(data.code)
        if (data.code === 500) {
          this.$alert('用户名密码校验失败，请检查')
        } else {
          this.$alert('服务器错误，请联系网站管理员')
        }
      })
    }
  },
  mounted () {
  }
}
</script>

<style lang="scss" scoped>
</style>
