<template>
  <div class="box">
    <div class="box_header">
      <div class="box_header_total">
        <span class="count">{{ candidate.length }}</span>
        <span class="label">Candidates</span>
      </div>
      <div class="box_header_info">
        <div class="item">
          <span class="item_top">All</span>
          <span class="item_botttom">Best In</span>
        </div>
        <div class="item">
          <span class="item_top">All</span>
          <span class="item_botttom">Best In</span>
        </div>
        <div class="item">
          <span class="item_top">Default</span>
          <span class="item_botttom">All Results</span>
        </div>
      </div>
    </div>

    <div class="box_body">
      <div class="box_body_column">
        <span class="label">Name</span>
        <span class="label">Resume Score</span>
        <span v-if="visible" class="label">Skill Match</span>
        <span class="label">Skill</span>
      </div>
      <div class="box_body_list-box">
        <CandidateList
          v-for="item in tableData"
          :key="item.userName"
          :data="item"
        />
      </div>
    </div>
    <div class="box_footer">
      <Pagination :totalPage="totalPage" @send-event="reset" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, ref, watch } from 'vue';
import { Candidate, candidate } from '@constants';
import CandidateList from './CandidateList.vue';
import Pagination from './Pagination.vue';

const tableData = ref<Array<Candidate>>([]);
const selectedPage = ref<number>(1);
const totalPage = ref<number>(0);
const totalCount = ref<number | undefined>();
const visible = ref(false);
const limit = ref<number>(10);
const Hide = 650;

const reset = (pageIdx: number) => {
  if (pageIdx === 0) selectedPage.value = 1;
  else selectedPage.value = pageIdx;
};

const disassemble = (index: number, data: Array<Candidate>, size: number) => {
  const res = new Array();
  for (let i = 0; i < data.length; i += size) {
    res.push(data.slice(i, i + size));
  }
  return res[index];
};

const getData = () => {
  totalCount.value = candidate !== undefined ? candidate.length : 0;
  totalPage.value =
    Math.ceil(totalCount.value / limit.value) !== 0
      ? Math.ceil(totalCount.value / limit.value)
      : 1;
  tableData.value = disassemble(selectedPage.value - 1, candidate, limit.value);
};

const checkVisible = () => {
  visible.value = window.innerWidth > Hide ? true : false;
};

watch(selectedPage, () => {
  getData();
});

onMounted(() => {
  getData();
  checkVisible();
  window.addEventListener('resize', checkVisible);
});

onUnmounted(() => {
  window.removeEventListener('resize', checkVisible);
});
</script>

<style lang="scss" scoped>
.box {
  @include flex-center;
  flex-direction: column;
  width: 100%;
  height: 100%;

  &_header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    padding-bottom: 12px;
    border-bottom: 1px solid $color-white-200;

    &_total {
      display: flex;
      flex-direction: column;
      align-items: flex-start;
      justify-content: center;

      .count {
        font-family: 'MBC1961GulimM';
        font-size: 18px;
      }

      .label {
        color: $color-white-200;
        font-family: 'SUITE-Regular';
      }
    }

    &_info {
      display: flex;
      align-items: center;
      gap: 40px;

      @media (max-width: 450px) {
        display: none;
      }

      .item {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        justify-content: center;

        &_top {
          font-family: 'MBC1961GulimM';
          color: $color-blue-100;
        }

        &_bottom {
          font-family: 'SUITE-Regular';
          color: $color-white-200;
        }
      }
    }
  }

  &_body {
    width: 100%;
    height: calc(100% - 92px);

    &_column {
      display: flex;
      align-items: center;
      gap: 20px;
      width: 100%;
      height: 60px;

      .label {
        flex: 1;
        display: flex;
        align-items: center;
        justify-content: flex-start;
        width: 25%;
        height: 100%;
        font-weight: 300;
        color: $color-white-200;
      }

      :first-child {
        padding-left: 16px;

        @media (max-width: 650px) {
          flex: 1.5;
        }
      }
    }

    &_list-box {
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
      width: 100%;
      height: calc(100% - 92px);
      gap: 12px;
      overflow-y: auto;
    }
  }
}
</style>
