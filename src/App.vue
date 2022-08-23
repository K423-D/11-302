<script setup lang="ts">
import { reactive, ref } from "vue";

type Entity = {
  main?: number;
  balcony?: number;
  single?: number;
  second?: number;
};

// const currentEntity = reactive<Entity>({
//   main: 10,
//   balcony: 20,
//   single: 30,
//   second: 40,
// });
// const prevEntity = reactive<Entity>({
//   main: 5,
//   balcony: 15,
//   single: 25,
//   second: 35,
// });
const currentEntity = reactive<Entity>({
  main: undefined,
  balcony: undefined,
  single: undefined,
  second: undefined,
});
const prevEntity = reactive<Entity>({
  main: undefined,
  balcony: undefined,
  single: undefined,
  second: undefined,
});
const result = reactive<Entity>({
  main: undefined,
  balcony: undefined,
  single: undefined,
  second: undefined,
});
// const total = ref<number | undefined>(100);
const total = ref<number | undefined>(undefined);
const errMsg = ref("");

const calculate = () => {
  if (
    !currentEntity.main ||
    !currentEntity.balcony ||
    !currentEntity.single ||
    !currentEntity.second
  ) {
    errMsg.value = "请完善当前读数信息";
    return;
  }
  if (
    !prevEntity.main ||
    !prevEntity.balcony ||
    !prevEntity.single ||
    !prevEntity.second
  ) {
    errMsg.value = "请完善上次读数信息";
    return;
  }
  if (!total.value) {
    errMsg.value = "请输入总电费";
    return;
  }
  errMsg.value = "";
  try {
    Object.keys(currentEntity).map((key) => {
      currentEntity[key] = parseFloat(`${currentEntity[key]}`);
    });
    Object.keys(prevEntity).map((key) => {
      prevEntity[key] = parseFloat(`${prevEntity[key]}`);
    });
    const t =
      currentEntity.main +
      currentEntity.balcony +
      currentEntity.single +
      currentEntity.second -
      (prevEntity.main +
        prevEntity.balcony +
        prevEntity.single +
        prevEntity.second);
    const r1 =
      parseFloat(((currentEntity.main - prevEntity.main) / t).toFixed(2)) *
      total.value;
    const r2 =
      parseFloat(
        ((currentEntity.balcony - prevEntity.balcony) / t).toFixed(2)
      ) * total.value;
    const r3 =
      parseFloat(((currentEntity.single - prevEntity.single) / t).toFixed(2)) *
      total.value;
    const r4 =
      parseFloat(((currentEntity.second - prevEntity.second) / t).toFixed(2)) *
      total.value;
    console.log(currentEntity, prevEntity);
    console.log(t, r1, r2, r3, r4);

    result.main = parseFloat(`${r1.toFixed(2)}`);
    result.balcony = parseFloat(`${r2.toFixed(2)}`);
    result.single = parseFloat(`${r3.toFixed(2)}`);
    result.second = parseFloat(`${r4.toFixed(2)}`);
  } catch (error) {
    errMsg.value = "未知异常，请检查输入的数值是否正确";
  }
};
</script>

<template>
  <div
    class="flex flex-col justify-start items-center w-screen min-h-screen mb-8"
  >
    <h1 class="text-2xl font-bold my-6">电费计算器</h1>
    <el-card class="box-card md:w-1/2 mx-4">
      <el-form label-position="right" :model="currentEntity">
        <!-- 当前 -->
        <h2 class="text-lg font-bold">当前读数：</h2>
        <div class="flex justify-center items-center flex-wrap">
          <div class="flex justify-between items-center">
            <el-form-item label="主" class="item mx-4">
              <el-input
                size="small"
                clearable
                placeholder="请输入数值"
                v-model="currentEntity.main"
              />
            </el-form-item>
            <el-form-item label="阳" class="item mx-4">
              <el-input
                size="small"
                clearable
                placeholder="请输入数值"
                v-model="currentEntity.balcony"
              />
            </el-form-item>
          </div>
          <div class="flex justify-between items-center">
            <el-form-item label="独" class="item mx-4">
              <el-input
                size="small"
                clearable
                placeholder="请输入数值"
                v-model="currentEntity.single"
              />
            </el-form-item>
            <el-form-item label="次" class="item mx-4">
              <el-input
                size="small"
                clearable
                placeholder="请输入数值"
                v-model="currentEntity.second"
              />
            </el-form-item>
          </div>
        </div>
        <!-- 上次 -->
        <h2 class="text-lg font-bold">上次读数：</h2>
        <div class="flex justify-center items-center flex-wrap">
          <div class="flex justify-between items-center">
            <el-form-item label="主" class="item mx-4">
              <el-input
                size="small"
                clearable
                placeholder="请输入数值"
                v-model="prevEntity.main"
              />
            </el-form-item>
            <el-form-item label="阳" class="item mx-4">
              <el-input
                size="small"
                clearable
                placeholder="请输入数值"
                v-model="prevEntity.balcony"
              />
            </el-form-item>
          </div>
          <div class="flex justify-between items-center">
            <el-form-item label="独" class="item mx-4">
              <el-input
                size="small"
                clearable
                placeholder="请输入数值"
                v-model="prevEntity.single"
              />
            </el-form-item>
            <el-form-item label="次" class="item mx-4">
              <el-input
                size="small"
                clearable
                placeholder="请输入数值"
                v-model="prevEntity.second"
              />
            </el-form-item>
          </div>
        </div>
        <!-- 电费 -->
        <h2 class="text-lg font-bold">总电费：</h2>
        <div class="flex justify-center items-center flex-wrap">
          <el-form-item label="总" class="item mx-4">
            <el-input
              size="small"
              placeholder="请输入数值"
              clearable
              v-model="total"
            />
          </el-form-item>
          <el-form-item>
            <el-button type="primary" plain round @click="calculate">
              计算
            </el-button>
          </el-form-item>
        </div>
      </el-form>
      <el-alert
        v-if="errMsg"
        :title="errMsg"
        type="error"
        show-icon
        :closable="false"
      />
    </el-card>
    <el-card class="box-card md:w-1/2 mx-4 mt-4">
      <h2 class="text-lg font-bold">计算方式：</h2>
      <p>
        房间电费 = ( 房间当前读数 - 房间上次读数 ) / ( 所有房间当前读数之和 -
        所有房间上次读数之和 ) * 总电费
      </p>
    </el-card>
    <el-card class="box-card md:w-1/2 mx-4 mt-4">
      <h2 class="text-lg font-bold">计算结果(单位：元)：</h2>
      <div class="flex flex-wrap justify-start items-center">
        <div class="mr-4 mt-2">
          <span>主：</span>
          <el-tag type="success" effect="plain" round>
            {{ result.main || "无结果" }}
          </el-tag>
        </div>
        <div class="mr-4 mt-2">
          <span>阳：</span>
          <el-tag type="info" effect="plain" round>
            {{ result.balcony || "无结果" }}
          </el-tag>
        </div>
        <div class="mr-4 mt-2">
          <span>独：</span>
          <el-tag type="warning" effect="plain" round>
            {{ result.single || "无结果" }}
          </el-tag>
        </div>
        <div class="mr-4 mt-2">
          <span>次：</span>
          <el-tag type="danger" effect="plain" round>
            {{ result.second || "无结果" }}
          </el-tag>
        </div>
      </div>
    </el-card>
  </div>
</template>
