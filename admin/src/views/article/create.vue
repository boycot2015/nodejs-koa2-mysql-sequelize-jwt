<template>
  <Form ref="articleData" :model="articleData" :rules="ruleValidate" :label-width="80">
    <FormItem label="文章标题" prop="title">
      <Input v-model="articleData.title" placeholder="title"></Input>
    </FormItem>
    <FormItem label="文章作者" prop="author">
      <Input v-model="articleData.author" placeholder="author"></Input>
    </FormItem>
    <FormItem label="文章图片" prop="banner">
      <Input v-model="articleData.banner" placeholder="banner"></Input>
    </FormItem>
    <FormItem label="文章分类" prop="category">
      <Select
        v-if="categoryList.length > 0"
        v-model="articleData.category"
        placeholder="Select category"
        style="position:relative;z-index: 9999">
        <Option
          v-for="(cate, key) in categoryList"
          :key="key"
          :value="cate.name">
          {{cate.name}}
        </Option>
      </Select>
    </FormItem>
    <FormItem label="文章简介" prop="introduce">
      <Input v-model="articleData.introduce" type="textarea" :autosize="{minRows: 2,maxRows: 5}"
             placeholder="introduce"></Input>
    </FormItem>
    <FormItem label="文章内容" prop="content">
      <mavon-editor v-model="articleData.content"/>
    </FormItem>
    <FormItem>
      <Button @click="handleReset('articleData')">重置</Button>
      <Button type="primary" style="margin-left: 8px" @click="handleSubmit('articleData')">创建</Button>
    </FormItem>
  </Form>
</template>
<script>
  import {mapState, mapActions} from 'vuex'

  export default {
    computed: {
      ...mapState({
        categoryList: state => state.category.categoryList
      })
    },
    data() {
      return {
        articleData: {
          title: '',
          author: '梁凤波',
          category: '',
          banner: '',
          introduce: '',
          content: ''
        },
        ruleValidate: {
          title: [
            {required: true, message: 'The name cannot be empty', trigger: 'blur'}
          ],
          author: [
            {required: true, message: 'Author cannot be empty', trigger: 'blur'}
          ],
          category: [
            {required: true, message: 'Please select the category', trigger: 'change'}
          ],
          banner: [
            {required: true, message: 'Banner cannot be empty', trigger: 'blur'}
          ],
          introduce: [
            {required: true, message: 'Introduce cannot be empty', trigger: 'blur'}
          ],
          content: [
            {required: true, message: 'Please enter a personal introduction', trigger: 'blur'},
            {type: 'string', min: 20, message: 'Introduce no less than 20 words', trigger: 'blur'}
          ]
        }
      }
    },
    created() {
      this.getCategory();

    },
    methods: {
      ...mapActions({
        createArticle: 'article/createArticle',
        getCategoryList: 'category/getCategoryList'
      }),

      // 获取分类
      async getCategory() {
        try {
          await this.getCategoryList();
        } catch (e) {

        }
      },

      // 提交
      handleSubmit(name) {
        this.$refs[name].validate(async (valid) => {
          if (valid) {
            try {
              await this.createArticle(this.articleData);
              this.$Message.success('创建文章成功');
              window.location.href = "/article/list";

            } catch (e) {
              this.$Message.error('创建文章失败')
            }
          } else {
            this.$Message.error('Fail!');
          }
        })
      },
      handleReset(name) {
        this.$refs[name].resetFields();
      }
    }
  }
</script>
