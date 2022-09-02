<template class="app-container">
    <my-table :data="goodslist">
        <template #header>
            <td>#</td>
            <td>商品名称</td>
            <td>价格</td>
            <td>标签</td>
            <td>操作</td>
        </template>
        <template #body="{row,index}">
            <td>{{index+1}}</td>
            <td>{{row.goods_name}}</td>
            <td>￥{{row.goods_price}}</td>
            <td>
                <input type="text" class="form-control form-control-sm form-ipt" v-if="row.inputVisible" v-focus v-model.trim="row.inputValue" @blur="onInputConfirm(row)" @keyup.enter="onInputConfirm(row)" @keyup.esc="row.inputValue=''" />
                <button class="btn btn-primary btn-sm" v-else @click="row.inputVisible=true">+Tag</button>
                <span class="badge badge-warning tag" v-for="tag in row.tags">{{tag}}</span>
            </td>
            <td>
                <button class="btn btn-danger" @click="onRemove(row.id)">删除</button>
            </td>
        </template>
    </my-table>
</template>

<script>
import MyTable from './components/MyTable.vue'

export default {
  name: 'App',
  data() {
    return {
      goodslist: []
    }
  },
  methods: {
    async getGoodsList() {
      const { data: res } = await this.$http.get('/api/goods')
      if (res.status !== 0) return console.log('获取列表数据失败！')
      this.goodslist = res.data
      console.log(this.goodslist)
    },
    onRemove(id) {
      this.goodslist = this.goodslist.filter((x) => x.id !== id)
    },
    onInputConfirm(row) {
      // 输入值存储在常量val中
      const val = row.inputValue
      //清空文本框的值
      row.inputValue = ''
      //隐藏文本框 显示tag
      row.inputVisible = false
      //如果文本框值为空或者存在重复标签 则不进行添加
      if (!val || row.tags.indexOf(val) !== -1) return
      row.tags.push(val)
    }
  },
  created() {
    this.getGoodsList()
  },
  directives: {
    focus(el) {
      el.focus()
    }
  },
  components: {
    MyTable
  }
}
</script>
<style lang="less" scoped>
.form-ipt {
  width: 80px;
  display: inline;
}
.tag {
  margin-left: 5px;
}
</style>
