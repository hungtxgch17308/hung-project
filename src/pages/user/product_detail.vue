<script>
import Default from "../../layouts/default.vue";
import {ref, onValue, set, push, child, query, orderByChild, equalTo, remove} from 'firebase/database';
import {database} from "../../firebase/firebase";
import Footer from "../../components/Footer.vue";
export default {
  name: 'Product Detail',
  components: {
    Default, 
    Footer
  },
  data() {
    return {
      picked: null,
      number: 1,
      currentProduct: null,
      comment: '',
      comments: [],
      isLiked: false,
      likeId: null
    }
  },
  computed: {
    images() {
      return [
        'https://salt.tikicdn.com/cache/400x400/ts/product/98/4a/8d/760a27ffaee3e069fb80201376c307da.png.webp',
        'https://salt.tikicdn.com/cache/100x100/ts/product/a6/1d/90/ae0dbb4b9243526e0c26c48f728a9a24.jpg.webp',
        'https://salt.tikicdn.com/cache/100x100/ts/product/2b/b9/92/51b41c8aa8f310a7a227e010a02eec70.jpg.webp',
        // 'https://salt.tikicdn.com/cache/100x100/ts/product/9d/e9/dc/258b1c584f81e4b9c2617c0e6dc3e89e.jpg.webp',
        // 'https://salt.tikicdn.com/cache/100x100/ts/product/83/c0/2b/4402b1d03d61c69dc3f1d3958c53abd1.jpg.webp'
      ]
    },
    reverseComment() {
      return this.comments.reverse()
    },
    typeButtonLike() {
      return this.isLiked ? 'info' : 'default';
    }

  },
  created() {

    const productRef = ref(database, `/products/${this.$route.params.name}`)

    onValue(productRef, snapshot => {
      this.currentProduct = snapshot.val();
    })

    const commentRef = ref(database, '/comments');

    onValue(commentRef, snapshot => {
      const val = snapshot.val();


      this.comments = Object.keys(val).filter(item => val[item].product_id === this.$route.params.name).map(item => ({
        ...val[item],
        id: item
      }))

    })

    const searchRef = query(ref(database, 'likes'), orderByChild('product_id'), equalTo(this.$route.params.name.toString()));

    onValue(searchRef, snapshot => {
      const val = snapshot.val();

      Object.keys(val).forEach(key => {
        if (val[key].user_id === this.$store.state.user.uid) {
          this.isLiked = true;
          this.likeId = key;
          console.log(this.likeId);
        }
      })
    })

  },
  methods: {
    addToCart() {
      // this.$store.commit("addToCart", {
      //   name: this.$route.params.name,
      //   number: this.number
      // });


      this.$store.dispatch("addToCart", {
        id: this.$route.params.name,
        number: this.number,
        name: this.currentProduct.name,
        price: this.currentProduct.price
      })

      this.$toast.success("Thêm vào giỏ hàng thành công");
    },
    submit() {
      push(child(ref(database), 'comments'), {
        content: this.comment,
        product_id: this.$route.params.name,
        user_id: this.$store.state.user.uid,
        email: this.$store.state.user.email
      })
      this.comment = '';
    },
    like() {
      if (!this.isLiked) {
        push(child(ref(database), 'likes'), {
          product_id: this.$route.params.name,
          user_id: this.$store.state.user.uid
        })
      } else {
        this.isLiked = false;
        remove(ref(database, `likes/${this.likeId}`));
      }
    }
  }
}
</script>

<template>
  <default>
    <div>
      <div>
        <img class="tw-mt-4 tw-h-80 tw-w-full"
             src="http://shophoaqua.vn/public/media/file/files/slider/banner-bottom.png" alt="">
      </div>
      <h1 class="tw-flex tw-justify-center tw-text-orange-500 tw-mt-3">Chi Tiết Sản Phẩm</h1>
      <div class="tw-container tw-px-20 tw-mt-20">
        <div class="tw-grid tw-grid-cols-12 tw-gap-3">
          <div class="tw-col-span-4">
            <div>
              <img class="tw-h-72 tw-w-full"
                   :src="currentProduct.url"
                   alt="">
            </div>
            <div class="tw-grid tw-grid-cols-3 tw-gap-3 tw-mt-5">

            </div>
          </div>
          <div class="tw-col-span-8 tw-mt-5">


            <div>
              <h1 class="tw-text-lime-600">Tên Sản Phẩm: {{ currentProduct.name }}</h1>
              <span class="tw-text-xl tw-text-yellow-600"> {{ currentProduct.price }} đ</span>
            </div>
            <div>
              <span class="tw-text-2xl">Nội dung:</span>
              <div>
                {{ currentProduct.description }}
              </div>
            </div>
            <div class="tw-col-span-8 tw-mt-5">
              <h2>Thêm sản phẩm vào giỏ hàng</h2>
              <div class="tw-flex tw-w-96 ">

                <n-input-number :min="Number(1)" v-model:value="number"/>
                <n-button @click="addToCart" type="primary">Thêm vào giỏ hàng</n-button>
              </div>
              <n-button @click="like" :type="typeButtonLike">
                <span v-if="isLiked">Bỏ thích sản phẩm này</span>
                <span v-else>Thích sản phẩm này</span>
              </n-button>
            </div>
          </div>
        </div>
      </div>
      <div class="tw-text-lime-700 tw-ml-20 tw-my-20 tw-h-full tw-text-xl tw-mx-40">
        <h1>Mô tả:</h1>
        <p>Nếu bạn thấy xe 3 gác đẩy 1 xe bưởi chạy ngang, trên xe đề giá 10 ngàn/3 quả, bạn mua ăn thử thì có khi bạn
          không ăn được, hoặc là ăn mà bạn có thể chê cả ngày không hết…</p>
        <p>Cùng thời điểm với giá đó, nhưng dòng bưởi da xanh ruột hồng sẽ có giá tại vườn khoảng 40 ngàn/kg, 1 trái
          khoảng 1kg – 2kg</p>
        <div class="tw-flex tw-justify-center tw-mt-5">
          <img
              :src="currentProduct.url"
              alt="">
        </div>
        <div>
          <p>Do đó, nếu bạn chưa từng ăn bưởi da xanh thì có lẽ bạn nên tìm ăn thử ngay nhé.</p>
          <p>Mình xin thêm vài dòng giới thiệu về loại bưởi này:</p>
          <p>Mình biết rất nhiều người thuộc hàng cao thủ võ lâm ở thành thị, chẳng còn cái gì không biết nhưng khi nhìn
            thấy bưởi da xanh thì như từ trên trời rơi xuống. Trước tiên họ hỏi bưởi còn sống xanh mà sao ăn được, rồi
            lại ruột sao nó màu hồng, có khi nào ăn bị đắng không, sao không có hột…</p>
          <div class="tw-flex tw-justify-center tw-mt-5">
            <img :src="currentProduct.url" alt="">
          </div>
        </div>

      </div>
      <div class="tw-mx-20">
        <div class="tw-flex">
          <n-input v-model:value="comment"/>
          <n-button @click="submit">Bình luận</n-button>
        </div>
        <div>
          <div v-for="item in reverseComment" class="tw-mt-3">
            <div class="tw-font-semibold">{{ item.email }}</div>
            <div>{{ item.content }}</div>
          </div>
        </div>
      </div>

    </div>
     <Footer/>
  </default>
</template>
