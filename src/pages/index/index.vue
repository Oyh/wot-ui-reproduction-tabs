<script setup lang="ts">
definePage({
  name: 'home',
  style: {
    navigationBarTitleText: 'WdTabs sticky + animated',
  },
})

const active = ref(0)
const width = ref(0)

// 只用于观察：H5 下把 tabs 的实际宽度读出来
function measure() {
  // #ifdef H5
  const el = document.querySelector('.wd-tabs')
  width.value = el ? Math.round(el.getBoundingClientRect().width) : 0
  // #endif
}

onMounted(() => {
  measure()
  setTimeout(measure, 300)
})
</script>

<template>
  <view>
    <view class="wot-p-extra-loose wot-text-label-main wot-text-text-secondary">
      依次点击下面的页签，观察这行宽度读数
    </view>
    <view class="wot-px-extra-loose wot-pb-loose wot-text-label-main wot-text-text-auxiliary">
      <!-- #ifdef H5 -->
      .wd-tabs 宽度：{{ width }}px
      <!-- #endif -->
      <!-- #ifndef H5 -->
      H5 下会显示 tabs 的实际宽度，小程序端看页签行是否变宽、页面能否左右拖动
      <!-- #endif -->
    </view>

    <WdTabs v-model="active" sticky animated @change="nextTick(measure)">
      <WdTab v-for="i in 10" :key="i" :title="`tab ${i}`">
        <view style="font-size: 48px">
          第 {{ i }} 页：这是一段普通的示例文字。
        </view>
      </WdTab>
    </WdTabs>
  </view>
</template>
