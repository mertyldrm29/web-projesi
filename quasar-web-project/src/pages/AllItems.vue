<template>
  <q-page class="center-content">
    <button @click="showCart" class="cart-button">Sepetim</button>
    <h5 class="text1">Tüm Ürünler</h5>
    <div class="product-container">
      <div v-for="product in products" :key="product.id" class="product-card">
        <img :src="product.image" alt="Product Image" class="product-image" />
        <div class="product-details">
          <p class="product-name">{{ product.name }}</p>
          <p class="product-price">{{ product.price }}</p>
        </div>
        <button @click="addToCart(product)" class="add-to-cart-button">
          Sepete Ekle
        </button>
      </div>
    </div>
    <template v-slot:append>
      <div v-if="loading"><q-spinner /> Fetching data...</div>
      <q-btn v-else @click="addToCart" round dense flat />
    </template>

    <q-dialog v-model="isCartVisible" position="top">
      <div>
        <h2 v-if="cart.length === 0">Sepetiniz Boş, Lütfen Ürün Ekleyiniz.</h2>
        <ul v-else>
          <li v-for="item in cart" :key="item.id">
            <img :src="item.image" alt="Product Image" class="product-image" />
            <div class="product-details">
              <p class="product-name">{{ item.name }}</p>
              <p class="product-price">{{ item.price }} TL</p>
              <p class="product-quantity">Adet: {{ item.quantity }}</p>
            </div>
          </li>
        </ul>
        <p v-if="cart.length > 0" class="total-price">
          Toplam Fiyat: {{ getTotalPrice() }} TL
        </p>
        <button
          v-if="cart.length > 0"
          @click="clearCart"
          class="clear-cart-button"
        >
          Sepeti boşalt
        </button>
        <button @click="hideCart" class="close-cart-button">Kapat</button>
      </div>
    </q-dialog>
  </q-page>
  <q-page-container>
    <keep-alive>
      <router-view />
    </keep-alive>
  </q-page-container>
</template>

<style>
.center-content {
  display: flex;
  height: 100%;
}

.text1 {
  margin-bottom: 1500px;
  font-weight: bold;
}

.product-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.product-card {
  width: 250px;
  margin: 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  text-align: center;
}

.product-image {
  width: 230px;
  height: 350px;
  object-fit: cover;
  margin-bottom: 10px;
}

.product-name {
  font-weight: bold;
}

.product-price {
  color: #000000;
}
.cart-button {
  position: absolute;
  top: 10px;
  right: 10px;
  padding: 10px;
  background-color: #3498db;
  color: #fff;
  border: none;
  border-radius: 10px;
  cursor: pointer;
}

.q-dialog {
  background-color: #fff;
  padding: 20px;
}

.q-dialog .product-image {
  width: 230px;
  height: 350px;
  object-fit: cover;
  margin-right: 10px;
}

.q-dialog .product-details {
  display: inline-block;
  vertical-align: top;
}

.q-dialog .product-name {
  font-weight: bold;
}

.q-dialog .product-price {
  color: #000;
}

.q-dialog .product-quantity {
  margin-top: 5px;
}

.q-dialog .total-price {
  text-align: right;
  margin-top: 10px;
}

.q-dialog .clear-cart-button {
  background-color: #e74c3c;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
}

.q-dialog .close-cart-button {
  margin-top: 10px;
}
</style>

<script>
import {
  collection,
  query,
  where,
  getDocs,
  doc,
  setDoc,
  addDoc,
  deleteDoc,
  onSnapshot,
} from "firebase/firestore";
import { defineComponent } from "vue";
export default defineComponent({
  name: "AllItems",
  data() {
    return {
      products: [
        {
          id: 1,
          name: "Kısa Kollu Parça Boyalı T-Shirt",
          price: 239.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/2/p/7242/511/251/7242511251_6_1_8.jpg?t=1685437623409&imwidth=750",
        },
        {
          id: 2,
          name: "Kapüşonlu Basic Sweatshirt",
          price: 459.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/2/p/8591/513/300/8591513300_2_1_8.jpg?t=1678981174338&imwidth=1024",
        },
        {
          id: 3,
          name: "Sloganlı Basic T-Shirt",
          price: 179.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/4230/351/800/4230351800_2_3_8.jpg?t=1677432742757&imwidth=1024",
        },
        {
          id: 4,
          name: "Cepli Denim Ceket",
          price: 759.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/2/p/8713/504/427/8713504427_2_1_8.jpg?t=1676883077998&imwidth=1024",
        },
        {
          id: 5,
          name: "Fermuarlı Ve Kapüşonlu Kolej Sweatshirt",
          price: 659.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/4596/316/800/4596316800_2_1_8.jpg?t=1669652080651&imwidth=1024",
        },
        {
          id: 6,
          name: "Kısa Kollu Peanuts Baskılı T-Shirt",
          price: 379.75,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/1/p/7242/352/519/7242352519_2_1_8.jpg?t=1685611981524&imwidth=1024 ",
        },
        {
          id: 7,
          name: "Renkli Basic Denim Ceket",
          price: 559.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/2/p/8715/550/505/8715550505_2_1_8.jpg?t=1678894213857&imwidth=1024 ",
        },
        {
          id: 8,
          name: "Basic Sweatshirt",
          price: 659.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/4597/300/629/4597300629_2_1_8.jpg?t=1677691417388&imwidth=1024",
        },
        {
          id: 9,
          name: "Bisiklet Yaka Kolej Sweatshirt",
          price: 429.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/4596/311/500/4596311500_2_1_8.jpg?t=1669128453574&imwidth=1024",
        },
        {
          id: 10,
          name: "Floklu New York Baskılı Sweatshirt",
          price: 599.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/2/p/4596/560/712/4596560712_2_1_8.jpg?t=1673856334245&imwidth=1024",
        },
        {
          id: 11,
          name: "Kolej Bomber Ceket",
          price: 1099.0,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/8715/344/401/8715344401_2_1_8.jpg?t=1669824851494&imwidth=1024",
        },
        {
          id: 12,
          name: "Grafik Baskılı Kısa Kollu T-Shirt",
          price: 179.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/1/p/7242/359/800/7242359800_2_1_8.jpg?t=1685616210646&imwidth=1024",
        },
        {
          id: 13,
          name: "Kapüşonlu Basic Sweatshirt",
          price: 459.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/2/p/8591/513/404/8591513404_2_1_8.jpg?t=1667899712441&imwidth=1024 ",
        },
        {
          id: 14,
          name: "Balıkçı Yaka Pamuklu Ceket",
          price: 759.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/1/p/7712/320/806/7712320806_2_1_8.jpg?t=1683641699004&imwidth=1024",
        },
        {
          id: 15,
          name: "Kapüşonlu Basic Sweatshirt",
          price: 459.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/2/p/8591/513/800/8591513800_2_1_8.jpg?t=1678969274184&imwidth=1024",
        },
        {
          id: 16,
          name: "Kısa Kollu AC/DC Baskılı T-Shirt",
          price: 399.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/4239/303/505/4239303505_2_1_8.jpg?t=1678728692265&imwidth=1024 ",
        },
        {
          id: 17,
          name: "Hafif Bomber Ceket",
          price: 599.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/2/p/4710/533/800/4710533800_2_1_8.jpg?t=1678120094686&imwidth=1024",
        },
        {
          id: 18,
          name: "Hokusai The Great Wave Sweatshirt",
          price: 799.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/4596/312/800/4596312800_2_1_8.jpg?t=1669981403413&imwidth=1024",
        },
        {
          id: 19,
          name: "Grafik Baskılı Kısa Kollu T-Shirt",
          price: 179.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/1/p/7241/343/620/7241343620_2_1_8.jpg?t=1685020643510&imwidth=1024",
        },
        {
          id: 20,
          name: "STWD Kapüşonlu Sweatshirt",
          price: 699.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/2/p/4596/567/420/4596567420_2_1_8.jpg?t=1676032911711&imwidth=1024 ",
        },
        {
          id: 21,
          name: "Floklu Kelebek Baskılı Sweatshirt",
          price: 699.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/2/p/4596/584/800/4596584800_2_1_8.jpg?t=1675943616701&imwidth=1024",
        },
        {
          id: 22,
          name: "Kolej Baskılı Sweatshirt",
          price: 459.99,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/4596/201/612/4596201612_2_1_8.jpg?t=1676546974207&imwidth=1024",
        },
        {
          id: 23,
          name: "Kapüşonlu Oversize Fit Sweatshirt",
          price: 559.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/4596/346/505/4596346505_2_1_8.jpg?t=1677434196663&imwidth=1024",
        },
        {
          id: 24,
          name: "Çiçek Desenli Kısa Kollu T-Shirt",
          price: 299.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/4239/358/250/4239358250_2_1_8.jpg?t=1680516846440&imwidth=1024",
        },
        {
          id: 25,
          name: "Kelebek Grafik Baskılı T-Shirt",
          price: 179.75,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/1/p/4230/385/500/4230385500_2_1_8.jpg?t=1677691294604&imwidth=1024",
        },
        {
          id: 26,
          name: "Rick And Morty Baskılı Kapüşonlu Sweatshirt",
          price: 659.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/2/p/8593/505/251/8593505251_2_1_8.jpg?t=1677242567079&imwidth=1024",
        },
        {
          id: 27,
          name: "Van Gogh Baskılı Kapüşonlu Sweatshirt",
          price: 659.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/2/p/4596/530/800/4596530800_2_1_8.jpg?t=1669021376663&imwidth=1024 ",
        },
        {
          id: 28,
          name: "Basic Bisiklet Yaka Oversize Sweatshirt",
          price: 359.99,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/8591/347/400/8591347400_2_1_8.jpg?t=1676902375415&imwidth=1024",
        },
        {
          id: 29,
          name: "Kısa Denim Ceket",
          price: 899.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/4710/308/406/4710308406_2_1_8.jpg?t=1680168796673&imwidth=1024",
        },
        {
          id: 30,
          name: "Yazılı Kısa Kollu T-Shirt",
          price: 179.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/1/p/7241/321/800/7241321800_2_1_8.jpg?t=1685003759141&imwidth=1024",
        },
        {
          id: 31,
          name: "Basic Comfort Fit Denim Ceket",
          price: 599.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/2/p/4710/540/800/4710540800_2_1_8.jpg?t=1685952763539&imwidth=1024 ",
        },
        {
          id: 32,
          name: "Soluk Efektli Basic Sweatshirt",
          price: 459.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/3593/337/620/3593337620_2_1_8.jpg?t=1678979328340&imwidth=1024",
        },
        {
          id: 33,
          name: "Kapüşonlu Basic Sweatshirt",
          price: 459.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/2/p/8591/513/250/02/8591513250_6_1_8.jpg?t=1663670134125&imwidth=563",
        },
        {
          id: 34,
          name: "Pamuklu Basic İnce Ceket",
          price: 899.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/4710/334/500/4710334500_2_1_8.jpg?t=1680077110702&imwidth=1024",
        },
        {
          id: 35,
          name: "Volkswagen Karavan Baskılı T-Shirt",
          price: 379.75,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/1/p/7241/365/805/7241365805_2_1_8.jpg?t=1685364055293&imwidth=1024",
        },
        {
          id: 36,
          name: "STWD Baskılı Kapüşonlu Sweatshirt",
          price: 599.95,
          image:
            " https://static.pullandbear.net/2/photos//2023/V/0/2/p/4596/595/800/03/4596595800_2_1_8.jpg?t=1675170908071&imwidth=1024",
        },
        {
          id: 37,
          name: "Van Gogh İmzalı T-Shirt",
          price: 379.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/1/p/7241/398/805/7241398805_2_1_8.jpg?t=1682341522703&imwidth=1024",
        },
        {
          id: 38,
          name: "Kapüşonlu ve Çizgili Ceket",
          price: 599.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/V/0/2/p/4710/524/400/4710524400_2_1_8.jpg?t=1678208321701&imwidth=1024",
        },
        {
          id: 39,
          name: "Boston Kolej Kapüşonlu Sweatshirt",
          price: 599.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/1/p/7593/370/401/7593370401_2_1_8.jpg?t=1684854973175&imwidth=1024",
        },
        {
          id: 40,
          name: "Basic Sweatshirt",
          price: 329.95,
          image:
            "https://static.pullandbear.net/2/photos//2023/I/0/1/p/8590/403/800/8590403800_2_4_8.jpg?t=1669221268862&imwidth=1024",
        },
      ],
      cart: [],
      isCartVisible: false,
      loading: false,
      updating: [],
      newProduct: "",
      product: [],
      checked: true,
      productColRef: "Products",
      unsub: {},
    };
  },
  async created() {
    await this.getProducts();
    this.updating = new Array(this.product.length).fill(false);
    const tasks = this.product;
    this.unsub = onSnapshot(
      doc(this.$db, this.productColRef, this.product[0].id),
      (doc) => {
        tasks[0] = { ...doc.data(), id: product[0].id };
        console.log("Current data: ", doc.data());
      }
    );
  },
  methods: {
    addToCart(product) {
      const existingProduct = this.cart.find((item) => item.id === product.id);
      if (existingProduct) {
        existingProduct.quantity++;
      } else {
        this.cart.push({ ...product, quantity: 1 });
      }
    },
    showCart() {
      this.isCartVisible = true;
    },
    hideCart() {
      this.isCartVisible = false;
    },
    clearCart() {
      this.cart = [];
    },
    getTotalPrice() {
      return this.cart.reduce(
        (total, item) => total + item.price * item.quantity,
        0
      );
    },
    async getProducts() {
      this.loading = true;
      this.product = [];

      const q = query(
        collection(this.$db, "Products"),
        where("done", "==", false)
      );
      const querySnapshot = await getDocs(q);

      querySnapshot.forEach((product) => {
        console.log({ ...product.data(), id: product.id });
        this.tasks.push({ ...product.data(), id: product.id });
      });

      this.loading = false;
    },
    async addTask() {
      this.loading = true;

      let product = {
        title: this.product,
        done: false,
      };
      const docRef = await addDoc(collection(this.$db, "Products"), product);
      product.id = docRef.id;
      this.product.push(product);
      this.loading = false;
    },
  },
});
</script>
