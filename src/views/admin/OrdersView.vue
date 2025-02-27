<template>
  <div class="container | p-3">
    <h1>訂單列表</h1>
    <table class="table">
      <thead>
        <tr>
          <th width="120">建立日期</th>
          <th width="120">訂單編號</th>
          <th width="240">購買品項</th>
          <th width="120">付款狀態</th>
          <th width="120">其他操作</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="order in orders" :key="order.id">
          <td>{{ new Date(order.create_at * 1000).toISOString().split('T')[0] }}</td>
          <td>{{ order.id }}</td>
          <td>
            <ul class="ps-0 mb-0">
              <li v-for="product in order.products" :key="product.id" class="list-unstyled">
                {{ `${product.product.title} * ${product.qty} ${product.product.unit}` }}
              </li>
            </ul>
          </td>
          <td>
            <!-- 利用圖案取代文字 -->
            <span v-if="order.is_paid" class="text-success">已付款</span>
            <span v-else>未付款</span>
          </td>
          <td>
            <button
              @click="open_modal(order, 'edit')"
              type="button"
              class="btn btn-sm btn-primary | me-1"
            >
              詳細資訊
            </button>
            <button @click="open_modal(order)" type="button" class="btn btn-sm btn-outline-danger">
              刪除
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- Pagination -->
    <div class="d-flex justify-content-end">
      <Pagination :pagination="pagination" @update="get_orders" />
    </div>
  </div>
  <!-- Modal -->
  <OrderModal ref="orderModal" @update="get_orders" />
  <DelOrderModal ref="delOrderModal" @update="get_orders" />
</template>

<script>
import OrderModal from '@/components/OrderModal.vue';
import DelOrderModal from '@/components/DelOrderModal.vue';
import Pagination from '@/components/PaginationComponent.vue';

export default {
  components: {
    OrderModal,
    DelOrderModal,
    Pagination,
  },
  data() {
    return {
      orders: [],
      pagination: {},
    };
  },
  methods: {
    get_orders(currentPage = 1) {
      const api = `${process.env.VUE_APP_BASE}/v2/api/${process.env.VUE_APP_PATH}/admin/orders?page=${currentPage}`;
      this.$http.get(api).then((res) => {
        if (res.data.success) {
          this.orders = res.data.orders;
          this.pagination = res.data.pagination;
        }
      });
    },
    open_modal(order, status) {
      if (status === 'edit') {
        this.$refs.orderModal.open_modal(order);
      } else {
        this.$refs.delOrderModal.open_modal(order);
      }
    },
  },
  mounted() {
    this.get_orders();
  },
};
</script>
