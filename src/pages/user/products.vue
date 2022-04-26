<script>
import Default from "../../layouts/default.vue";
import axios from "axios";
import CardProduct from "../../components/CardProduct.vue";
import API from "../../ network";
import {onValue, getDatabase, ref, query, limitToLast, startAt, orderByChild, endAt} from "firebase/database";
import {database} from "../../firebase/firebase";

export default {
  name: 'ProductsPage',
  components: {
    Default,
    CardProduct
  },
  data() {
    return {
      sort: 'default',
      originProducts: [],
      categories: [],
      currentCategory: null,
      search: null,
      idTimeout: null
    }
  },
  computed: {
    optionsForSort() {
      return [
        {
          value: 'default',
          label: 'Thứ tự mặc định'
        },
        {
          value: 'rating',
          label: 'Thứ tự lượt đánh giá'
        },
        {
          value: 'price-asc',
          label: 'Thứ tự theo lượt giá tăng'
        },
        {
          value: 'price-desc',
          label: 'Thứ tự theo lượt giá giảm'
        },
        {
          value: 'popular',
          label: 'Thứ tự phổ biến'
        }
      ]
    },
    products() {
      let clone = [...this.originProducts];
      
      if (this.currentCategory !== null) {
        clone = clone.filter(item => item.category_id === this.currentCategory)
      }

      return clone;
    }
  },
  created() {
    const productsRef = ref(database, 'products');

    onValue(productsRef, snapshot => {
      const val = snapshot.val();

      this.originProducts = Object.keys(val).map(item => ({
            ...val[item],
            id: item
          })
      )
    })

    const categoriesRef = ref(database, '/categories');
    onValue(categoriesRef, snapshot => {

      const val = snapshot.val();

      if (!val) return;

      this.categories = Object.keys(val).map(item => ({
        ...val[item],
        id: item
      }));

    })

  },
  watch: {
    search(value) {
      clearTimeout(this.idTimeout);

      this.idTimeout = setTimeout(() => {
        const searchRef = query(ref(database, 'products'), orderByChild('name'), startAt(value), endAt(this.search + "\uf8ff"));

        onValue(searchRef, snapshot => {
          const val = snapshot.val();
          if (!val) {
            this.originProducts = []
            return;
          }
          this.originProducts = Object.keys(val).map(item => ({
                ...val[item],
                id: item
              })
          )
        })
      }, 1000)

    }
  },
  methods: {
    pickCategory(newCategory) {
      if (newCategory === this.currentCategory) {
        this.currentCategory = null;
        return;
      }
      this.currentCategory = newCategory;
    }
  }

}
</script>

<template>
  <Default>
    <div>
         <span class="tw-text-xl tw-flex tw-justify-center tw-mt-5 tw-text-orange-700">Tổng số sản phẩm</span>
    </div>
    
    <div class="tw-container tw-px-20">
      <div class="tw-grid tw-grid-cols-12 tw-gap-10 tw-mt-5">
        <div class="tw-col-span-8">
          <div class="tw-flex tw-justify-between">
         
            <div class="tw-flex tw-ml-5 tw-w-80">
              <n-input v-model:value="search" placeholder="Search Products"/>
            </div>
           
            <n-select v-model:value="sort" :options="optionsForSort" class="tw-w-3/12"/>
          </div>

          <div class="tw-grid tw-grid-cols-4 tw-gap-7">
            <CardProduct v-for="(item,index) in products" :key="item.id" :star="index" :name="item.name"
                         :price="item.price"
                         :url="item.url"/>

          </div>
        </div>

        <div class="tw-col-span-4 tw-mt-10">
          
          <h2 class="tw-text-amber-500">Click to choose Categories</h2>
    <div>
    <div class="flex justify-center">
  <div>
 


    <div class="tw-mt-3">
  <div class="tw-mt-2 tw-flex tw-flex-col-reverse">
    <div class=" tw-flex tw-flex-col-reverse">
    
      <span class="tw-ml-2 " 
      v-for="item in categories"  :key="item.id" :class="currentCategory === item.id ? 'tw-text-blue-500' : ''"  @click="pickCategory(item.id)"
      > <div class="tw-flex tw-py-2">
          <input type="radio" class="tw-form-radio tw-mt-1 tw-mr-1" name="accountType" value="personal">
          <div>
            {{item.name}}
          </div>
        </div>
        </span>
    </div>
    
     <!-- <label class="tw-inline-flex tw-items-center tw-ml-6">
      <input type="radio" class="tw-form-radio" name="accountType" value="busines">
      <span class="tw-ml-2">Business</span>
    </label> -->
  </div>
</div>
   
  </div>
</div>
</div>
<div class="tw-mt-3">
            <img src="https://media.istockphoto.com/photos/vegetarian-food-in-string-bag-picture-id1311051864?b=1&k=20&m=1311051864&s=170667a&w=0&h=_i5_EgoXJVASRnWtoJFLNsoddM-5UmXazmVbNrpIPLE=" alt="">
          </div>
        </div>

      </div>
    </div>



  </Default>
</template>