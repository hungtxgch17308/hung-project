<script>
import API from "../../../ network";
import {database} from "../../../firebase/firebase";
import {ref, onValue, get, child, remove} from 'firebase/database';
import store from "../../../store";

export default {
  name: 'ListCategories',
  beforeRouteEnter(to, from, next) {
    if (!store.state.isAuthAdmin) {
      next('/admin/login');
      return;
    }
    next();
  },
  data() {
    return {
      categories: [],

    }
  },
  created() {

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

  // created(){
  //   const nameRef = ref(database, '/users');
  //   onValue(nameRef, snapshot => {

  //      const val1 = snapshot.val();
  //      if (!val1) return;

  //      this.users = object.keys(val1),map(item => ({
  //        ...val1[item],
  //        id: item
  //      }))

  //   })
  // },

  methods: {
    async deleteCategory(id) {
      const result = await this.$swal({
        icon: 'warning',
        title: 'Bạn có chắc muốn xóa danh mục này không',
        showCancelButton: true,
        reverseButtons: true
      });

      if (!result.isConfirmed) return;

      await remove(ref(database, `/categories/${id}`));
      this.categories = this.categories.filter(item => item.id !== id);
      this.$toast.success("Xóa thành công");

    },


  }
}
</script>

<template>
  <div class="tw-container tw-px-20 tw-mt-10">
    <div class="tw-flex tw-justify-start ">
      <router-link class="tw-no-underline" to="/admin/categories/create">
        <n-button  type="primary">Thêm mới loại sản phẩm</n-button>
      </router-link>
    </div>
    <n-table class="tw-mt-5">
      <thead>
      <tr>
        <th>Số thứ tự</th>
        <th>Tên</th>
        <th>Thao tác</th>
      </tr>
      </thead>

      <template v-if="categories.length > 0">
        <tbody>
        <tr v-for="(item,index) in categories" :key="item.id">
          <td>{{ index + 1 }}</td>
          <td>{{ item.name }}</td>

          <td>
            <router-link class="tw-no-underline" :to="`/admin/categories/${item.id}/edit`">
              <n-button class="tw-no-underline" type="info">Sửa</n-button>
            </router-link>
            <n-button @click="deleteCategory(item.id)" class="tw-no-underline tw-ml-5 " type="error">Xóa</n-button>
          </td>
        </tr>
        </tbody>
      </template>
      <template v-else>
        <div class="tw-flex tw-w-full tw-justify-center">
          <n-empty description="Không có danh mục"/>
        </div>
      </template>

    </n-table>






  </div>
</template>
