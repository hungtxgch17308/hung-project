<script>
export default {
  name: 'CardProduct',
  props: ['star', 'name', 'price', 'sold', 'url', 'id']
}
</script>

<template>
  <router-link :to="`/product/${id}`"
               class="tw-no-underline tw-text-black tw-p-5 hover:tw-shadow-lg tw-transition tw-duration-500 tw-cursor-pointer tw-rounded ">
    <div>
      <img class="tw-object-fit tw-w-full tw-h-60"
           :src="url"
           alt="">
    </div>
    <div>
      {{ name }}
    </div>
    <div class=" tw-flex tw-divide-x-2">
      <star-rating
          star-size="20"
          :show-rating="false"
          :rating="Number(star)"
          :read-only="true"
      />
      <div>
        <n-divider vertical/>
      </div>
      <div>
        <span>Đã bán {{ sold }}</span>
      </div>
    </div>
    <div>
      <span class="tw-text-xl tw-font-bold">{{ price }} đ</span>
    </div>


  </router-link>
</template>

<style scoped lang="scss">
.n-divider.n-divider--vertical {
  @apply tw-bg-gray-400;
}
</style>