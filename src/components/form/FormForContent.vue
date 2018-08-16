<template>
<div>
  <!-- 添加推送 -->
  <el-form
    label-width="100px"
    :model="formData"
    label-position="right"
    :rules="rules"
    ref="formData"
    class="dialog-form push-dialog"
  >
    <!-- 文章标题 -->
    <el-form-item label="文章标题" prop="title">
      <el-row :span="24">
        <el-col :span="20">
          <el-input v-model="formData.title" size="small"></el-input>
        </el-col>
        <el-col :span="4">
          <el-button class="preview" type="primary" size="small" @click="preview">预览</el-button>
        </el-col>
      </el-row>
    </el-form-item>
    <!-- 所属频道 -->
    <el-form-item label="所属频道" prop="type">
      <el-select v-model="formData.type" placeholder="请选择所属频道" size="small">
        <el-option
          v-for="item in pushTypes"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        >
        </el-option>
      </el-select>
    </el-form-item>
    <!-- 所属内容 -->
    <el-form-item label="所属内容" prop="content">
      <el-select v-model="formData.content" placeholder="请选择所属内容" size="small">
        <el-option
          v-for="item in contentTypes"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        >
        </el-option>
      </el-select>
    </el-form-item>
    <!-- 文章作者 -->
    <el-form-item label="文章作者" prop="author">
      <el-input v-model="formData.author" size="small"></el-input>
    </el-form-item>
    <!-- 文章内容-->
    <el-form-item label="文章内容">
      <div id="editor" v-model="formData.body">
        <!--<div id="toolbar" class="toolbar"></div>-->
        <!--<div id="text" class="text" v-model="formData.body">-->
          <!--{{formData.body}}-->
        <!--</div>-->
      </div>
      <p class="tip">注:只能上传jpg/png格式文件，文件不能超过500K，尺寸999*999最少上传三张图片</p>
    </el-form-item>

    <el-form-item label="是否推荐文章">
      <template>
        <el-radio v-model="formData.reward" :label="true">是</el-radio>
      </template>
    </el-form-item>

    <div class="dialog-footer">
      <el-button size="small" @click="cancel('formData')">取消</el-button>
      <el-button type="primary" size="small" @click="submit('formData')">保存</el-button>
    </div>
  </el-form>
</div>
</template>
<script>
export default {
  name: 'FormForContent',
  props: ['formData', 'control'],
  data () {
    return {
      // 所属频道
      pushTypes: [
        {
          value: '0',
          label: '营销'
        },
        {
          value: '1',
          label: '文章'
        }
      ],
      // 内容类型
      contentTypes: [
        {
          value: '0',
          label: '全部内容'
        },
        {
          value: '1',
          label: '精品内容'
        },
        {
          value: '2',
          label: '平台内容'
        },
        {
          value: '3',
          label: '普通内容'
        }
      ],
      editor: {},
      rules: {
        title: [
          { required: true, message: '请填写文章标题', trigger: 'blur' }
        ],
        type: [
          { required: true, message: '请选择所属频道', trigger: 'change' }
        ],
        content: [
          { required: true, message: '请选择所属内容', trigger: 'change' }
        ],
        author: [
          { required: true, message: '请填写文章作者', trigger: 'blur' }
        ]
      }
    }
  },
  mounted () {
    var E = require('wangeditor');// 使用 npm 安装
    // 创建编辑器
    this.editor = new E('#editor');
    // this.editor = new E('#toolbar','#text');
    this.editor.customConfig.uploadImgServer = 'http://mgr.janesi.com/admin/bops/file/upload';
    // this.editor.customConfig.uploadImgServer = 'http://yun1.janesi.net/html/video/img';
    // 提示
    this.editor.customConfig.uploadImgHooks = {
      before: function (xhr, editor, files) {
        // 图片上传之前触发
        // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象files是选择的图片文件
        // 如果返回的结果是 {prevent: true, msg: 'xxxx'} 则表示用户放弃上传
        // return {
        //     prevent: true,
        //     msg: '放弃上传'
        // }
      },
      success: function (xhr, editor, result) {
        // 图片上传并返回结果，图片插入成功之后触发
        // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象，result 是服务器端返回的结果
        // console.log(xhr, editor, result)
        console.log(result);
        console.log(xhr)
      },
      fail: function (xhr, editor, result) {
        // 图片上传并返回结果，但图片插入错误时触发
        // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象，result 是服务器端返回的结果
      },
      error: function (xhr, editor) {
        // 图片上传出错时触发
        // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象
        console.log(xhr)
        console.log(editor)
      },
      timeout: function (xhr, editor) {
        // 图片上传超时时触发
        // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象
      },

      // 如果服务器端返回的不是 {errno:0, data: [...]} 这种格式，可使用该配置
      // （但是，服务器端返回的必须是一个 JSON 格式字符串！！！否则会报错）
      customInsert: function (insertImg, result, editor) {
        // 图片上传并返回结果，自定义插入图片的事件（而不是编辑器自动插入图片！！！）
        // insertImg 是插入图片的函数，editor 是编辑器对象，result 是服务器端返回的结果

        // 举例：假如上传图片成功后，服务器端返回的是 {url:'....'} 这种格式，即可这样插入图片：
        console.log(insertImg);
        console.log(result);
        console.log(editor);
        var url = result.result[0];
        insertImg(url)
        // result 必须是一个 JSON 格式字符串！！！否则报错
      }
    };
    this.editor.customConfig.uploadImgShowBase64 = true;
    this.editor.create();
  },
  watch: {
    'formData.body': function (val, old) {
      this.editor.txt.html(this.formData.body)
    },
    'control': function (val, old) {
      if (val) {
        this.formData.body = ''
      }
    }
  },
  methods: {
    // 预览
    preview () {
      this.$emit('openPreview', this.editor.txt.html())
    },

    // 关闭弹窗
    cancel (ruleForm) {
      this.$emit('cancelDialog', false);
      this.$refs[ruleForm].resetFields()
    },

    // 提交回传表单信息给父组件
    submit (ruleForm) {
      var s = this.editor.txt.html();
      var n = (s.split('img')).length - 1;
      this.$refs[ruleForm].validate((valid) => {
        if (valid) {
          // if (n < 3) {
          //   this.$message('最少上传三张图')
          // }
          this.formData.body = s;
          console.log(this.formData)
          // success
        } else {
          return false
        }
      })
    }
  }
}
</script>

<style scoped>
.dialog-form {
  margin-bottom: 80px
}

.push-dialog {
  padding-right: 15px
}
.preview{
  margin-left: 20px;
}
.tip{
  color:#ccc;
}
#text{
  height:300px;
  border: 1px solid #ccc;
  overflow: hidden;
  overflow-y: scroll;
  padding: 30px;
}
.text::-webkit-scrollbar {
  display: none;
}
</style>
