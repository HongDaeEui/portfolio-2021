<template>
  <div>
    <!-- Banner Area -->
    <section id="common_banner_one">
      <div class="container ">
        <div class="row">
          <div class="col-lg-12">
            <div class="common_banner_text">
              <h2>{{ this.title }}</h2>
              <b-breadcrumb
                :items="breadcrumbItems"
                class="bg-transparent"
              ></b-breadcrumb>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Shop Main Area -->
    <section id="shop_main_area" class="ptb-100">
      <div class="container">
        <div class="row">
          <div class="col-lg-2">
            <div class="shop_sidebar_boxed">
              <h4>Brand</h4>
              <form>
                <label class="custom_boxed"
                  >GUCCI
                  <input
                    type="radio"
                    name="radio"
                    v-model="brand"
                    value="구찌"
                    @change="brandFilter"
                  />
                  <span class="checkmark"></span>
                </label>
                <label class="custom_boxed"
                  >Hermes
                  <input
                    type="radio"
                    name="radio"
                    v-model="brand"
                    value="에르메스"
                    @change="brandFilter"
                  />
                  <span class="checkmark"></span>
                </label>
                <label class="custom_boxed"
                  >Louis Vuitton
                  <input
                    type="radio"
                    name="radio"
                    v-model="brand"
                    value="루이비통"
                    @change="brandFilter"
                  />
                  <span class="checkmark"></span>
                </label>
                <label class="custom_boxed"
                  >TAG Heuer
                  <input
                    type="radio"
                    name="radio"
                    v-model="brand"
                    value="태그호이어"
                    @change="brandFilter"
                  />
                  <span class="checkmark"></span>
                </label>
                <label class="custom_boxed"
                  >Chanel
                  <input type="radio" name="radio" />
                  <span class="checkmark"></span>
                </label>
                <label class="custom_boxed"
                  >SAINT LAURENT
                  <input type="radio" name="radio" />
                  <span class="checkmark"></span>
                </label>
                <label class="custom_boxed"
                  >Dior
                  <input type="radio" name="radio" />
                  <span class="checkmark"></span>
                </label>
                <div class="clear_button">
                  <button
                    type="button"
                    class="theme-btn-one btn_sm btn-black-overlay"
                    @click="clearFilter"
                  >
                    Clear Filter
                  </button>
                </div>
              </form>
            </div>
          </div>

          <div class="col-lg-10 text-center">
            <div class="btn-group" role="group" aria-label="...">
              <button type="button" class="btn btn-light" @click="allFilter">
                All
              </button>
              <button type="button" class="btn btn-light" @click="bagFilter">
                Bag
              </button>
              <button type="button" class="btn btn-light" @click="watchFilter">
                Watch
              </button>
            </div>

            <div class="shop_Search col-lg-3">
              <form>
                <input
                  type="text"
                  class="form-control"
                  v-model="searchValue"
                  placeholder="Search..."
                />
                <button type="button" @click="searchFilter">
                  <img
                    :src="require('@/assets/img/svg/search.svg')"
                    alt="img"
                  />
                </button>
              </form>
            </div>
            <div class="row">
              <div
                class="col-lg-4 col-md-4 col-sm-6 col-12"
                v-for="productItem in list"
                id="my-table"
                :key="productItem.id"
                :items="productItems"
                :per-page="perPage"
                :current-page="currentPage"
                small
              >
                <ProductBox
                  :key="productItem.id"
                  :productId="productItem.product_id"
                  :productImg1="productItem.image_main"
                  :productImg2="productItem.image_main"
                  :productTagClass="productItem.productTagClass"
                  :productTag="productItem.productTag"
                  :productTitle="productItem.product_name"
                  :productPrice="productItem.rent_price"
                  :productDescription="productItem.productDescription"
                />
              </div>

              <div class="col-lg-12">
                <!-- pagination start -->
                <b-pagination
                  v-model="currentPage"
                  pills
                  :total-rows="rows"
                  :per-page="perPage"
                  aria-controls="my-table"
                ></b-pagination>
                <!-- pagination end -->
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Instagram Arae -->
    <InstagramArea />
  </div>
</template>

<script>
import ProductBox from "~/components/product-box/ProductBox";
import InstagramArea from "~/components/instagram/InstagramArea";
import ShopSidebar from "~/components/widgets/ShopSidebar";

export default {
  name: "shop-left-sidebar",
  components: {
    ProductBox,
    InstagramArea,
    ShopSidebar,
  },
  data() {
    return {
      list: [],
      perPage: 6,
      currentPage: 1,
      title: "Shop",
      // Product Items Data
      productItems: [],

      // Breadcrumb Items Data
      breadcrumbItems: [
        {
          text: "Home",
          to: "/",
        },
        {
          text: "Shop",
          to: "/shop/shopfilter",
        },
      ],

      // Pagination Data
      // rows: 0,
      currentPage: 1,
      totalPage: 0,
      searchValue: "",
      brand: "",
      bag: "BAG",
      watch: "WATCH",
    };
  },
  watch: {
    currentPage() {
      this.getProductList();
    },
  },
  created() {
    this.initPage();
  },
  mounted() {
    this.getProductList();
  },
  methods: {
    async getProductList() {
      this.list = await this.$api(
        `api/products?pageNo=${this.currentPage}&productBrand=${this.brand}`,
        "get",
        {}
      );
      console.log(this.list);
    },
    searchFilter() {
      console.log(this.searchValue);
    },

    brandFilter() {
      // this.list = this.list.filter((u) => u.product_brand == this.brand);
      // console.log(this.brand);
      this.getProductList();
    },
    clearFilter() {
      this.brand = "";
      this.getProductList();
    },

    allFilter() {
      this.getProductList();
    },

    bagFilter() {
      this.list = this.list.filter((u) => u.product_tag == this.bag);
    },
    watchFilter() {
      this.list = this.list.filter((u) => u.product_tag == this.watch);
    },

    initPage() {
      //총 제품 등록수 - 23
      //
      this.totalPage = 12;
      // this.totalPage = Math.ceil(totalCount / this.perPage);
      console.log(this.totalPage);
    },
  },
  computed: {
    rows() {
      // return this.items.length;
      return this.totalPage;
    },
  },

  // Page head() Title, description for SEO
  head() {
    return {
      title: this.title,
      meta: [
        {
          hid: "description",
          name: "description",
          content: "Shop page - AndShop Ecommerce Vue js, Nuxt js Template",
        },
      ],
    };
  },
};
</script>

<style></style>
