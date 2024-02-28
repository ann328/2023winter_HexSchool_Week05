<template>
  <div class="container">
    <div class="mt-4">
      <!-- 產品Modal -->
      <productMoreModal
        :temp-product="tempProduct"
        :add-product-cart="addProductCart"
        ref="moreModal"
      ></productMoreModal>
      <!-- 產品Modal -->
      <table class="table align-middle">
        <thead>
          <tr>
            <th>圖片</th>
            <th>商品名稱</th>
            <th>價格</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="product in products" :key="product.id">
            <td style="width: 200px">
              <div
                style="height: 100px; background-size: cover; background-position: center"
                :style="{ backgroundImage: `url(${product.imageUrl})` }"
              ></div>
            </td>
            <td>{{ product.title }}</td>
            <td>
              <div class="h5" v-if="product.origin_price === product.price">
                {{ product.origin_price }} 元
              </div>
              <div v-else>
                <del class="h6">原價 {{ product.origin_price }} 元</del>
                <div class="h5">現在只要 {{ product.price }} 元</div>
              </div>
            </td>
            <td>
              <div class="btn-group btn-group-sm">
                <button
                  type="button"
                  class="btn btn-outline-secondary"
                  @click="openProductMoreModal(product)"
                >
                  <i class="fas fa-spinner fa-pulse"></i>
                  查看更多
                </button>
                <button
                  type="button"
                  class="btn btn-outline-danger"
                  @click="addProductCart(product.id)"
                  :disabled="product.id === addProductCartLoading"
                >
                  <span
                    class="spinner-border spinner-border-sm"
                    aria-hidden="true"
                    v-if="product.id === addProductCartLoading"
                  ></span>
                  <i class="fas fa-spinner fa-pulse"></i>
                  加到購物車
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
      <!-- 購物車列表 -->
      <div class="text-end">
        <button class="btn btn-outline-danger" type="button" @click="delProductCartAll">
          清空購物車
        </button>
      </div>
      <table class="table align-middle">
        <thead>
          <tr>
            <th></th>
            <th>品名</th>
            <th style="width: 150px">數量/單位</th>
            <th>單價</th>
          </tr>
        </thead>
        <tbody>
          <template v-if="cart.carts">
            <tr v-for="cartProduct in cart.carts" :key="cartProduct.id">
              <td>
                <button
                  type="button"
                  class="btn btn-outline-danger btn-sm"
                  @click="delProductCartProduct(cartProduct.id)"
                >
                  <i class="fas fa-spinner fa-pulse"></i>
                  x
                </button>
              </td>
              <td>
                {{ cartProduct.product.title }}
                <!-- <div class="text-success">已套用優惠券</div> -->
              </td>
              <td>
                <div class="input-group input-group-sm">
                  <div class="input-group mb-3">
                    <input
                      min="1"
                      type="number"
                      class="form-control"
                      v-model.number="cartProduct.qty"
                      @change="changeCartProductQty(cartProduct, cartProduct.qty)"
                      :disabled="cartProduct.id === changeCartProductQtyLoading"
                    />
                    <span class="input-group-text" id="basic-addon2">{{
                      cartProduct.product.unit
                    }}</span>
                  </div>
                </div>
              </td>
              <td class="text-end">
                <!-- <del> {{ cartProduct.product.origin_price * cartProduct.qty }}</del>
                <small class="text-success">折扣價：</small> -->
                {{ cartProduct['final_total'] }}
              </td>
            </tr>
          </template>
        </tbody>
        <tfoot>
          <tr>
            <td colspan="3" class="text-end">總計</td>
            <td class="text-end">{{ cart.total }}</td>
          </tr>
          <tr>
            <td colspan="3" class="text-end text-success">折扣價</td>
            <td class="text-end text-success">{{ cart['final_total'] }}</td>
          </tr>
        </tfoot>
      </table>
    </div>
  </div>
  <order-form @reset-chart="getCart"></order-form>
</template>

<script>
const { VITE_API, VITE_PATH } = import.meta.env

import productMoreModal from '../components/productMoreModal.vue'
import orderForm from '../components/orderForm.vue'

export default {
  data() {
    return {
      products: [],
      tempProduct: {},
      cart: {},
      addProductCartLoading: '',
      changeCartProductQtyLoading: ''
    }
  },
  methods: {
    getProducts() {
      this.axios
        .get(`${VITE_API}/api/${VITE_PATH}/products`)
        .then((res) => {
          this.products = res.data.products
        })
        .catch((err) => {
          alert(err.response.data.message)
        })
    },
    openProductMoreModal(product) {
      this.tempProduct = product
      this.$refs.moreModal.productMoreModalUse.show()
    },
    getCart() {
      this.axios
        .get(`${VITE_API}/api/${VITE_PATH}/cart`)
        .then((res) => {
          this.cart = res.data.data
        })
        .catch((err) => {
          alert(err.response.data.message)
        })
    },
    addProductCart(productId, qty = 1) {
      this.addProductCartLoading = productId
      this.axios
        .post(`${VITE_API}/api/${VITE_PATH}/cart`, { data: { product_id: productId, qty: qty } })
        .then((res) => {
          alert(res.data.message)
          this.getCart()
          this.$refs.moreModal.productMoreModalUse.hide()
          this.addProductCartLoading = ''
        })
        .catch((err) => {
          alert(err.response.data.message)
        })
    },
    changeCartProductQty(cartItem, qty = 1) {
      this.changeCartProductQtyLoading = cartItem.id
      this.axios
        .put(`${VITE_API}/api/${VITE_PATH}/cart/${cartItem.id}`, {
          data: { product_id: cartItem.product_id, qty: qty }
        })
        .then((res) => {
          alert(res.data.message)
          this.changeCartProductQtyLoading = ''
          this.getCart()
        })
        .catch((err) => {
          alert(err.response.data.message)
        })
    },
    delProductCartProduct(productId) {
      this.axios
        .delete(`${VITE_API}/api/${VITE_PATH}/cart/${productId}`)
        .then((res) => {
          alert(res.data.message)
          this.getCart()
        })
        .catch((err) => {
          alert(err.response.data.message)
        })
    },
    delProductCartAll() {
      this.axios
        .delete(`${VITE_API}/api/${VITE_PATH}/carts`)
        .then((res) => {
          alert(res.data.message)
          this.getCart()
        })
        .catch((err) => {
          alert(err.response.data.message)
        })
    }
  },
  mounted() {
    this.getProducts()
    this.getCart()
  },
  components: {
    productMoreModal,
    orderForm
  }
}
</script>
