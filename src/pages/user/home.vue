<script>
import Default from "../../layouts/default.vue";
import CardProduct from "../../components/CardProduct.vue";
import {database} from "../../firebase/firebase";
import {ref, onValue} from "firebase/database";
import Footer from "../../components/Footer.vue";
export default {
  name: 'HomePage',
  components: {
    Default,
    CardProduct,
    Footer
  },
  data() {
    return {
      products: []
    }
  },
  created() {

    const productsRef = ref(database, 'products');

    onValue(productsRef, snapshot => {
      const val = snapshot.val();

      this.products = Object.keys(val).map(item => ({
        ...val[item],
        id: item
      }))
    }, (err) => console.log('err', err))
  }
}
</script>

<template>


  <Default>

    <div class="tw-flex tw-justify-center tw-pt-5 tw-text-orange-600">
      <h1>BEST SALE PRODUCTS</h1>
    </div>
    <div class="tw-grid tw-grid-cols-5 tw-gap-6 tw-p-10">
      <CardProduct :id="item.id" v-for="(item,index) in products" :key="item.id" :star="index" :name="item.name"
                   :price="item.price"
                   :url="item.url"/>
    </div>

    <div class="tw-bg-slate-200 tw-mx-20 tw-red tw-h-max">
      <div>
        <h2 class="tw-text-purple-700 tw-flex tw-justify-center"> 3 reasons why should buy fruits at "HOA QUA SACH"</h2>
        <div class="tw-grid tw-grid-cols-3 tw-gap-6 tw-p-5 tw-justify-items-center">
          <div class="tw-h-40">
            <div class="tw-flex tw-justify-center">
              <img src="https://hoaquafuji.com/storage/app/media/t1_3.png" alt="">
            </div>

            <div class="tw-pt-3 tw-px-10">
              <span class="tw-flex tw-justify-center">HOA QUẢ TƯƠI SẠCH</span>
              <p> Luôn đặt lợi ích cho người tiêu dùng lên hàng đầu.</p>
            </div>
          </div>

          <div>
            <div class="tw-flex tw-justify-center">
              <img src="https://hoaquafuji.com/storage/app/media/t1_4.png" alt="">
            </div>
            <div class="tw-pt-3 tw-px-10">
              <span class="tw-flex tw-justify-center">HOA QUẢ TƯƠI SẠCH</span>
              <p>Đổi trả miễn phí trong 24h khi khách hàng không hài lòng</p>
            </div>
          </div>

          <div>
            <div class="tw-flex tw-justify-center">
              <img src="https://hoaquafuji.com/storage/app/media/t1_5.png" alt="">
            </div>

            <div class="tw-pt-3 tw-px-10">
              <span class="tw-flex tw-justify-center">HOA QUẢ TƯƠI SẠCH</span>
              <p>Quy trình nhập hàng, vận chuyển, bảo quản chuyên nghiệp.</p>
            </div>
          </div>

        </div>
      </div>
    </div>


    <div>
      <div class="tw-flex tw-justify-center tw-pt-10">
        <img src="https://hoaquafuji.com/themes/hoaquafuji/assets/img/border.png" alt="">
      </div>
    </div>
    <div class="tw-grid tw-grid-cols-3 tw-gap-6 tw-p-5 tw-justify-items-center tw-h-full ">
      <div>
        <h1 class="tw-flex tw-justify-center tw-text-amber-600">ĐÔI NÉT VỀ FUJI</h1>
        <p class="tw-w-64" style="word-break: break-all;">With the motto "Bringing customers not only safe and
          high-quality fruit products, but also friendly convenient services. "Fuji Import and Export Joint Stock
          Company" - a unit specializing in importing high-class fruits from countries around the world is gradually
          developing and winning the trust of Vietnamese consumers. Currently, the company has a system of stores under
          the brand name Fuji Clean Fruits, which are widely distributed throughout the Northern provinces to serve the
          needs of all customers. With constant efforts over time, the Fuji Clean Fruit system is getting better every
          day in all aspects.</p>
      </div>
      <div>
        <img class="tw-flex tw-justify-center" src="https://hoaquafuji.com/themes/hoaquafuji/assets/img/t2_img.png"
             alt="">
      </div>
      <div>
        <h1 class="tw-flex tw-justify-center tw-text-amber-600 ">ĐÔI NÉT VỀ FUJI</h1>
        <p class="tw-w-64" style="word-break: break-all;">With the motto "Bringing customers not only safe and
          high-quality fruit products, but also friendly convenient services. "Fuji Import and Export Joint Stock
          Company" - a unit specializing in importing high-class fruits from countries around the world is gradually
          developing and winning the trust of Vietnamese consumers. Currently, the company has a system of stores under
          the brand name Fuji Clean Fruits, which are widely distributed throughout the Northern provinces to serve the
          needs of all customers. With constant efforts over time, the Fuji Clean Fruit system is getting better every
          day in all aspects.</p>
      </div>
    </div>
     <Footer/>
  </Default>

</template>


