<script setup>
import { ref, reactive, inject } from 'vue';
import { ElMessage } from 'element-plus';
import TimeDR from '备份/src/DRUtils/TimeDR.js';
import CategoryAPI from '备份/src/api/CategoryAPI';

const injectData = inject('provideData'); //注入祖先组件提供的数据
const injectFuncGetList = inject('provideFuncGetList'); //注入祖先组件提供的方法

const data = reactive({
  //数据
  name: '',
  sort: '0',
  status: '1',
});

const add = () => {
  //添加
  if (data.name == '') {
    ElMessage.error('请填写名称');
    return;
  }

  let values = {
    parent_id: injectData.parentId,
    level: injectData.level,
    name: data.name,
    sort: data.sort,
    status: data.status,
    create_time: TimeDR.now(),
  };
  //console.log(values)

  CategoryAPI.add(values)
    .then((result) => {
      //console.log(result)

      if (!result.status) {
        ElMessage.error(result.msg);
        return;
      }

      injectData.pageAdd = false; //关闭对话框
      injectFuncGetList(); //重新获取列表
    })
    .catch((err) => {
      console.log('err:', err);
    });
};

const close = () => {
  //关闭对话框
  data.name = '';
  data.sort = '0';
  data.status = '1';
};
</script>

<template>
  <el-dialog v-model="injectData.pageAdd" draggable @close="close" width="600" title="添加类别">
    <el-form label-width="80">
      <el-form-item label="名称">
        <el-input v-model="data.name" placeholder="请填写名称" />
      </el-form-item>

      <el-form-item label="排序">
        <el-input v-model="data.sort" />
      </el-form-item>

      <el-form-item label="状态">
        <el-radio-group v-model="data.status">
          <el-radio value="1">显示</el-radio>
          <el-radio value="0">隐藏</el-radio>
        </el-radio-group>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="add">添加</el-button>
      </el-form-item>
    </el-form>
  </el-dialog>
</template>

<style scoped></style>
