<script>
import useCart from "@/composables/cart.js";

export default {
  name: "App",

  setup() {
    const {cartProducts, removeProduct, cartTotal, totalQty} = useCart()
    return {cartProducts, removeProduct, cartTotal, totalQty}
  },

  mounted() {
    $(document).trigger('changed')
    this.getCategories()
    this.getToken()
  },

  watch: {
    $route(to, from) {
      this.getToken()
    }
  },

  data() {
    return {
      categories: [],
      token: null,
    }
  },

  methods: {
    getCategories() {
      axios.get('/api/categories')
          .then(res => {
            this.categories = res.data.data
          })
    },

    getToken() {
      this.token = localStorage.getItem('x_xsrf_token')
    },

    logout() {
      axios.post('/api/users/logout')
          .then(res => {
            localStorage.removeItem('x_xsrf_token')
            this.token = null
            this.$router.push({name: 'user.login'})
          })
    },

    closeOffcanvasCart() {
      document.getElementById('closeOffcanvasCart').click()
    },

    closeOffcanvasNavbar() {
      document.getElementById('openOffcanvasNavbar').click()
    },
  }
}
</script>

<template>
  <div class="wrapper">
    <header class="header">
      <div class="header-top py-1">
        <div class="container">
          <div class="row">
            <div class="col-6 col-sm-4">
              <div class="header-top-phone d-flex align-items-center h-100">
                <i class="fa-solid fa-mobile-screen"></i>
                <a href="tel:+1234567890" class="ms-2">123-456-7890</a>
              </div>
            </div>

            <div class="col-sm-4 d-none d-sm-block">
              <ul class="social-icons d-flex list-unstyled justify-content-center">
                <li><a href="#"><i class="fa-brands fa-youtube"></i></a></li>
                <li><a href="#"><i class="fa-brands fa-facebook-f"></i></a></li>
                <li><a href="#"><i class="fa-brands fa-instagram"></i></a></li>
              </ul>
            </div>

            <div class="col-6 col-sm-4">
              <div class="header-top-account d-flex justify-content-end">
                <div class="btn-group">
                  <div class="dropdown">
                    <button class="btn btn-sm dropdown-toggle" type="button"
                            data-bs-toggle="dropdown" aria-expanded="false">
                      Аккаунт
                    </button>
                    <ul class="dropdown-menu">
                      <li v-if="!token">
                        <router-link :to="{ name: 'user.login' }" class="dropdown-item">Вход</router-link>
                      </li>
                      <li v-if="!token">
                        <router-link :to="{ name: 'user.register' }" class="dropdown-item">Регистрация</router-link>
                      </li>
                      <li v-if="token"><a class="dropdown-item" href="#" @click.prevent="logout">Выйти</a></li>
                    </ul>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div><!-- ./header-top -->

      <div class="header-middle bg-white py-4">
        <div class="container">
          <div class="row align-items-center">
            <div class="col-sm-6">
              <router-link :to="{ name: 'main' }" class="header-logo h1">ElectroDom</router-link>
            </div>

            <div class="col-sm-6 mt-2 mt-md-0">
              <form>
                <div class="input-group">
                  <input type="text"
                         class="form-control"
                         name="search"
                         placeholder="Searching..."
                         aria-label="Searching..."
                         aria-describedby="button-search">
                  <button class="btn btn-outline-warning" type="submit" id="button-search"><i
                      class="fa-solid fa-magnifying-glass"></i></button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div><!-- ./header-middle -->
    </header><!-- ./header top + middle -->

    <div class="header-bottom sticky-top" id="header-nav">
      <nav class="navbar navbar-expand-lg bg-dark" data-bs-theme="dark">
        <div class="container">
          <button class="navbar-toggler" type="button" data-bs-toggle="offcanvas"
                  data-bs-target="#offcanvasNavbar" aria-controls="offcanvasNavbar" aria-expanded="false"
                  aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
          </button>

          <div class="offcanvas offcanvas-start" id="offcanvasNavbar" tabindex="-1"
               aria-labelledby="offcanvasNavbarLabel">
            <div class="offcanvas-header">
              <h5 class="offcanvas-title" id="offcanvasNavbarLabel">Меню</h5>
              <button type="button" class="btn-close" data-bs-dismiss="offcanvas"
                      aria-label="Close"></button>
            </div>
            <div class="offcanvas-body">
              <ul class="navbar-nav">

                <li class="nav-item dropdown menu">
                  <button class="nav-link dropdown-toggle" id="openOffcanvasNavbar" role="button"
                          data-bs-toggle="dropdown"
                          aria-expanded="false" data-bs-auto-close="outside">
                    Каталог
                  </button>
                  <ul class="dropdown-menu">
                    <li v-for="category in categories" class="nav-item dropend category-menu">

                      <!-- Zero-level nesting categories  -->
                      <router-link :to="{ name: 'category.show', params: {id: category.id} }"
                                   @click="closeOffcanvasNavbar"
                                   class="category-link">
                        {{ category.title }}
                      </router-link>

                      <!-- Categories of the first level of nesting  -->
                      <template v-if="category.sub_categories">
                        <button class="dropdown-item dropdown-toggle category-open-submenu" data-bs-toggle="dropdown"
                                data-bs-auto-close="outside">
                          <span class="icon-open-submenu"></span>
                        </button>
                        <ul class="dropdown-menu dropdown-menu-end">
                          <li v-for="sub_category in category.sub_categories" class="nav-item dropdown category-menu">
                            <router-link :to="{ name: 'category.show', params: {id: sub_category.id} }"
                                         @click="closeOffcanvasNavbar"
                                         class="category-link">
                              {{ sub_category.title }}
                            </router-link>

                            <!-- Categories of the second level of nesting  -->
                            <template v-if="sub_category.sub_categories">
                              <button class="dropdown-item dropdown-toggle category-open-submenu"
                                      data-bs-toggle="dropdown"
                                      data-bs-auto-close="outside">
                                <span class="icon-open-submenu"></span>
                              </button>
                              <ul class="dropdown-menu dropdown-menu-end">
                                <li v-for="sub_sub_category in sub_category.sub_categories">
                                  <router-link :to="{ name: 'category.show', params: {id: sub_sub_category.id} }"
                                               @click="closeOffcanvasNavbar"
                                               class="dropdown-item">
                                    {{ sub_sub_category.title }}
                                  </router-link>
                                </li>
                              </ul>
                            </template>
                          </li>
                        </ul>
                      </template>
                    </li>
                  </ul>
                </li><!-- ./categories -->

                <li class="nav-item">
                  <a class="nav-link active" aria-current="page" href="index.html">Доставка</a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="#">Оплата</a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="#">О нас</a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="#">Контакты</a>
                </li>
              </ul>
            </div>
          </div><!-- ./offcanvas header-bottom -->

          <div>
<!--            <a href="#" class="btn p-1">
              <i class="fa-solid fa-heart"></i>
              <span class="badge text-bg-warning cart-badge bg-warning rounded-circle">3</span>
            </a>-->

            <button v-if="!cartProducts.length" class="btn p-1" type="button">
              <i class="fa-solid fa-cart-shopping"></i>
              <span class="badge text-bg-warning cart-badge bg-warning rounded-circle">{{ totalQty }}</span>
            </button>

            <button v-if="cartProducts.length" class="btn p-1" id="cart-open" type="button" data-bs-toggle="offcanvas"
                    data-bs-target="#offcanvasCart"
                    aria-controls="offcanvasCart">
              <i class="fa-solid fa-cart-shopping"></i>
              <span class="badge text-bg-warning cart-badge bg-warning rounded-circle">{{ totalQty }}</span>
            </button>
          </div>
        </div>
      </nav><!-- ./navbar -->

      <div class="offcanvas offcanvas-end" tabindex="-1" id="offcanvasCart"
           aria-labelledby="offcanvasCartLabel">
        <div class="offcanvas-header">
          <h5 class="offcanvas-title" id="offcanvasCartLabel">Корзина</h5>
          <button type="button" class="btn-close" data-bs-dismiss="offcanvas"
                  id="closeOffcanvasCart" aria-label="Close"></button>
        </div>
        <div class="offcanvas-body">
          <div class="table-responsive-sm">
            <table class="table offcanvasCart-table">
              <tbody>
              <tr v-for="product in cartProducts">
                <td class="product-img-td">
                  <a href="#">
                    <img :src="product.image" alt="">
                  </a>
                </td>
                <td class="product-title-td">
                  <router-link :to="{ name: 'product.show', params: {id: product.id} }"
                               @click="closeOffcanvasCart"
                               class="text-size-sm">
                    <small>{{ product.title }}</small></router-link>
                </td>
                <td><small>{{ product.price }}</small></td>
                <td>&times;{{ product.qty }}</td>
                <td>
                  <button @click="removeProduct(product.id)" class="btn btn-danger"><i
                      class="fa-regular fa-circle-xmark"></i></button>
                </td>
              </tr>
              </tbody>
              <tfoot>
              <tr>
                <td colspan="3" class="text-end">Общая цена:</td>
                <td colspan="2" class="text-end">{{ cartTotal }}</td>
              </tr>
              <tr>
                <td v-if="cartProducts.length" colspan="5" class="text-end pt-4" style="border-bottom-width: 0;">
                  <router-link :to="{ name: 'cart.index' }" @click="closeOffcanvasCart"
                               class="btn btn-outline-warning me-1">
                    Корзина
                  </router-link>
                  <router-link :to="{ name: 'checkout.index' }" @click="closeOffcanvasCart"
                               class="btn btn-outline-secondary">
                    Оформить
                  </router-link>
                </td>
              </tr>
              </tfoot>
            </table>
          </div>


          <!--Пример кнопок, закрывающих панель и делающих скролл-->
          <!--<div class="dropdown mt-3">
              <button class="btn btn-secondary dropdown-toggle" type="button"
                      data-bs-toggle="dropdown">
                  Dropdown button
              </button>
              <ul class="dropdown-menu">
                  <li><a class="dropdown-item closecart" data-href="footer">Footer</a></li>
                  <li><a class="dropdown-item closecart" data-href="about">About</a></li>
                  <li><a class="dropdown-item closecart" data-href="map">Map</a></li>
              </ul>
          </div>-->
        </div>
      </div><!-- ./offcanvas cart -->

    </div><!-- ./header-bottom -->

    <router-view :key="this.$route.params.id"></router-view>

    <footer class="footer" id="footer">
      <div class="container">
        <div class="row">
          <div class="col-md-3 col-sm-6 col-12">
            <h4>Информация</h4>
            <ul class="list-unstyled">
              <li><a href="index.html">Главная</a></li>
              <li><a href="#">Оплата</a></li>
              <li><a href="#">Доставка</a></li>
              <li><a href="#">Контакты</a></li>
            </ul>
          </div>

          <div class="col-md-3 col-sm-6 col-12">
            <h4>Часы работы</h4>
            <ul class="list-unstyled">
              <li>Сыктывкар, ул.Кирова, д.1</li>
              <li>пн-пт: 9:00 - 18:00</li>
            </ul>
          </div>

          <div class="col-md-3 col-sm-6 col-12">
            <h4>Контакты</h4>
            <ul class="list-unstyled">
              <li><a href="tel:+1234567890">123-456-7890</a></li>
              <li><a href="tel:+0987654321">098-765-4321</a></li>
            </ul>
          </div>

          <div class="col-md-3 col-sm-6 col-12">
            <h4>Мы в соц.сетях</h4>
            <ul class="footer-icons">
              <li><a href="#"><i class="fa-brands fa-youtube"></i></a></li>
              <li><a href="#"><i class="fa-brands fa-facebook-f"></i></a></li>
              <li><a href="#"><i class="fa-brands fa-instagram"></i></a></li>
            </ul>
          </div>
        </div>
      </div>
    </footer><!-- ./footer -->
  </div><!-- ./wrapper -->
</template>

<style>

</style>