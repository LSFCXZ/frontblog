<template>
  <div class="layui-container fly-marginTop">
    <div class="fly-panel fly-panel-user"
      pad20>
      <div class="layui-tab layui-tab-brief"
        lay-filter="user">
        <ul class="layui-tab-title">
          <li>
            <router-link :to="{name:'login'}">登入</router-link>
          </li>
          <li class="layui-this">找回密码
            <!--重置密码-->
          </li>
        </ul>
        <div class="layui-form layui-tab-content"
          id="LAY_ucm"
          style="padding: 20px 0;">
          <validation-observer ref="observer"
            v-slot="{ validate }">
            <div class="layui-tab-item layui-show">
              <!-- 重置密码 -->
              <!--
          <div class="fly-msg">{{d.username}}，请重置您的密码</div>
          <div class="layui-form layui-form-pane"  style="margin-top: 30px;">
            <form action="/user/repass" method="post">
              <div class="layui-form-item">
                <label for="L_pass" class="layui-form-label">密码</label>
                <div class="layui-input-inline">
                  <input type="password" id="L_pass" name="pass" required lay-verify="required" autocomplete="off" class="layui-input">
                </div>
                <div class="layui-form-mid layui-word-aux">6到16个字符</div>
              </div>
              <div class="layui-form-item">
                <label for="L_repass" class="layui-form-label">确认密码</label>
                <div class="layui-input-inline">
                  <input type="password" id="L_repass" name="repass" required lay-verify="required" autocomplete="off" class="layui-input">
                </div>
              </div>
              <div class="layui-form-item">
                <label for="L_vercode" class="layui-form-label">人类验证</label>
                <div class="layui-input-inline">
                  <input type="text" id="L_vercode" name="vercode" required lay-verify="required" placeholder="请回答后面的问题" autocomplete="off" class="layui-input">
                </div>
                <div class="layui-form-mid">
                  <span style="color: #c00;">{{d.vercode}}</span>
                </div>
              </div>
              <div class="layui-form-item">
                <input type="hidden" name="username" value="{{d.username}}">
                <input type="hidden" name="email" value="{{d.email}}">
                <button class="layui-btn" alert="1" lay-filter="*" lay-submit>提交</button>
              </div>
            </form>
          </div>

          <div class="fly-error">该重置密码链接已失效，请重新校验您的信息</div>
          <div class="fly-error">非法链接，请重新校验您的信息</div>
          -->

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
                          placeholder="请输入用户名"
                          v-model="username"
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
                      @click="validate().then(submit)">提交</button>
                  </div>
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
import { forget } from '@/api/login'
export default {
  name: 'Forget',
  mixins: [Code],
  data () {
    return {
      username: ''

    }
  },
  methods: {
    async submit () {
      // 判断前端验证表单是否全部通过
      const isValid = await this.$refs.observer.validate()
      if (!isValid) {
        return
      }
      // 忘记密码，发送邮件
      forget({
        username: this.username,
        code: this.code
      }).then((res) => {
        if (res.code === 200) {
          alert('发送成功')
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
</style>
