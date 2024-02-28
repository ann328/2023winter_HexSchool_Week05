<template>
  <div
    class="modal fade"
    id="productMoreModal"
    tabindex="-1"
    role="dialog"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
    ref="productMoreModal"
  >
    <div class="modal-dialog modal-xl" role="document">
      <div class="modal-content border-0">
        <div class="modal-header bg-dark text-white">
          <h5 class="modal-title" id="exampleModalLabel">
            <span>{{ tempProduct.title }}</span>
          </h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          <div class="row">
            <div class="col-sm-6">
              <template v-for="(img, key) in tempProduct.imagesUrl" :key="tempProduct.id">
                <img class="img-fluid" :src="img" alt="" />
              </template>
            </div>
            <div class="col-sm-6">
              <span class="badge bg-primary rounded-pill">{{ tempProduct.category }}</span>
              <p>商品描述：{{ tempProduct.description }}</p>
              <p>商品內容：{{ tempProduct.content }}</p>
              <div class="h5" v-if="!tempProduct.price">{{ tempProduct.origin_price }} 元</div>
              <del class="h6" v-if="tempProduct.price">原價 {{ tempProduct.origin_price }} 元</del>
              <div class="h5" v-if="tempProduct.price">現在只要 {{ tempProduct.price }} 元</div>
              <div>
                <div class="input-group">
                  <input type="number" class="form-control" v-model.number="qty" min="1" />
                  <button
                    type="button"
                    class="btn btn-primary"
                    @click="addProductCart(tempProduct.id, qty)"
                  >
                    加入購物車
                  </button>
                </div>
              </div>
            </div>
            <!-- col-sm-6 end -->
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import * as bootstrap from 'bootstrap'

export default {
  props: ['tempProduct', 'addProductCart'],
  data() {
    return {
      productMoreModalUse: null,
      qty: 1
    }
  },
  mounted() {
    this.productMoreModalUse = new bootstrap.Modal(this.$refs.productMoreModal, {
      keyboard: false,
      backdrop: 'static'
    })
  },
  watch: {
    tempProduct() {
      this.qty = 1
    }
  }
}
</script>
