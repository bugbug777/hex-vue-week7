<template>
  <div class="container | p-3">
    <h1 class="mb-4">產品列表</h1>
    <div class="text-end | mb-4">
      <button @click="open_modal('new')" type="button" class="btn btn-primary">建立新的產品</button>
    </div>
    <table class="table | mb-4">
      <thead>
        <tr>
          <th width="120">分類</th>
          <th>產品名稱</th>
          <th width="120">原價</th>
          <th width="120">售價</th>
          <th width="100">是否啟用</th>
          <th width="120">編輯</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in products" :key="product.id">
          <td>{{ product.category }}</td>
          <td>{{ product.title }}</td>
          <td>{{ product.origin_price }}</td>
          <td>{{ product.price }}</td>
          <td>
            <span v-if="product.is_enabled" class="text-success">啟用</span>
            <span v-else>未啟用</span>
          </td>
          <td>
            <div class="btn-group">
              <button
                @click="open_modal('edit', product)"
                type="button"
                class="btn btn-outline-primary btn-sm"
              >
                編輯
              </button>
              <button
                @click="open_modal('delete', product)"
                type="button"
                class="btn btn-outline-danger btn-sm"
              >
                刪除
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <div class="d-flex justify-content-end">
      <Pagination :pagination="pagination" @update="get_products" />
    </div>
  </div>
  <!-- Modal -->
  <ProductModal ref="productModal" @update="get_products" />
  <DelProductModal ref="delProductModal" @update="get_products" />
</template>

<script>
import ProductModal from '@/components/ProductModal.vue';
import DelProductModal from '@/components/DelProductModal.vue';
import Pagination from '@/components/PaginationComponent.vue';

export default {
  components: {
    ProductModal,
    DelProductModal,
    Pagination,
  },
  data() {
    return {
      products: [],
      pagination: {},
    };
  },
  methods: {
    get_products(currentPage = 1) {
      const api = `${process.env.VUE_APP_BASE}/v2/api/${process.env.VUE_APP_PATH}/admin/products?page=${currentPage}`;
      this.$http.get(api).then((res) => {
        if (res.data.success) {
          this.products = res.data.products;
          this.pagination = res.data.pagination;
        }
      });
    },
    open_modal(status, item) {
      if (status === 'new') {
        this.$refs.productModal.open_modal(status);
      } else if (status === 'edit') {
        this.$refs.productModal.open_modal(status, item);
      } else {
        this.$refs.delProductModal.open_modal(item);
      }
    },
  },
  mounted() {
    this.get_products();
  },
};
</script>
