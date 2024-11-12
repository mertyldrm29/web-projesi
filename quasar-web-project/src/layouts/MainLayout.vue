<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="header">
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />
        <!-- Sağ üst köşeye yerleştirilen buton -->
        <q-btn
          @click="openMenu"
          icon="person"
          color="primary"
          round
          dense
          class="fixed-top-right q-mr-md q-mt-md"
        >
        </q-btn>

        <!-- Açılır Menü -->
        <q-menu v-model="menuVisible" anchor="top right" self="top right">
          <q-card style="min-width: 300px">
            <q-card-section>
              <h5 class="textgiris">Giriş Yap / Kayıt Ol</h5>

              <!-- Giriş Yap ve Kayıt Ol Butonları -->
              <div v-if="formType === ''">
                <q-btn
                  @click="switchToLogin"
                  color="primary"
                  label="Giriş Yap"
                  dense
                  class="full-width"
                />
                <q-btn
                  @click="switchToRegister"
                  color="secondary"
                  label="Kayıt Ol"
                  dense
                  class="full-width"
                />
              </div>

              <!-- Giriş Yap Formu -->
              <div v-if="formType === 'login'">
                <q-btn
                  @click="switchToInitial"
                  icon="arrow_back"
                  label="Geri Dön"
                  color="secondary"
                  dense
                  class="full-width q-mb-sm"
                />
                <q-form @submit.prevent="login">
                  <q-input
                    filled
                    v-model="email"
                    label="E-posta"
                    type="email"
                    dense
                    required
                  />
                  <q-input
                    filled
                    v-model="password"
                    label="Şifre"
                    type="password"
                    dense
                    required
                  />
                  <q-card-actions>
                    <q-btn
                      type="submit"
                      color="primary"
                      label="Giriş Yap"
                      dense
                      @click="loginCheck"
                    />
                  </q-card-actions>
                </q-form>
              </div>

              <!-- Kayıt Ol Formu -->
              <div v-if="formType === 'register'">
                <q-btn
                  @click="switchToInitial"
                  icon="arrow_back"
                  label="Geri Dön"
                  color="secondary"
                  dense
                  class="full-width q-mb-sm"
                />
                <q-form @submit.prevent="register">
                  <q-input
                    filled
                    v-model="firstName"
                    label="İsim"
                    dense
                    required
                  />
                  <q-input
                    filled
                    v-model="lastName"
                    label="Soyisim"
                    dense
                    required
                  />
                  <q-input
                    filled
                    v-model="email"
                    label="E-posta"
                    type="email"
                    dense
                    required
                  />
                  <q-input
                    filled
                    v-model="password"
                    label="Şifre"
                    type="password"
                    dense
                    required
                  />
                  <q-card-actions>
                    <q-btn
                      type="submit"
                      color="secondary"
                      label="Kayıt Ol"
                      dense
                      @click="registerCheck"
                    />
                  </q-card-actions>
                </q-form>
              </div>

              <!-- Uyarı Mesajı -->
              <p v-if="message" :style="{ color: messageColor }">
                {{ message }}
              </p>
            </q-card-section>
          </q-card>
        </q-menu>
      </q-toolbar>
      <div class="logo-container">
        <img
          src="https://static.pullandbear.net/2/static2/img/headLogo/logo_pull_white_new.svg"
          class="logo-image"
        />
      </div>
    </q-header>
    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      :width="240"
      :breakpoint="500"
      class="drawer"
    >
      <q-scroll-area class="fit">
        <q-list padding class="menu-list">
          <q-item to="/" exact clickable v-ripple>
            <q-item-section avatar>
              <q-icon name="list_alt" />
            </q-item-section>

            <q-item-section> Tüm Ürünler </q-item-section>
          </q-item>

          <q-item to="/filtre" exact clickable v-ripple>
            <q-item-section avatar>
              <q-icon name="filter_alt" />
            </q-item-section>

            <q-item-section> Filtrele </q-item-section>
          </q-item>

          <q-item to="/giris" exact clickable v-ripple>
            <q-item-section avatar>
              <q-icon name="login" />
            </q-item-section>

            <q-item-section> ~Mert Yıldırım 210101070 </q-item-section>
          </q-item>
        </q-list>
      </q-scroll-area>
    </q-drawer>

    <q-page-container>
      <keep-alive>
        <router-view />
      </keep-alive>
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from "vue";
import EssentialLink from "components/EssentialLink.vue";


const linksList = [
  {
    title: "Docs",
    icon: "school",
    caption: "quasar.dev",
    link: "https://quasar.dev",
  },
  {
    title: "Github",
    caption: "github.com/quasarframework",
    icon: "code",
    link: "https://github.com/quasarframework",
  },
  {
    title: "Discord Chat Channel",
    caption: "chat.quasar.dev",
    icon: "chat",
    link: "https://chat.quasar.dev",
  },
  {
    title: "Forum",
    caption: "forum.quasar.dev",
    icon: "record_voice_over",
    link: "https://forum.quasar.dev",
  },
  {
    title: "Twitter",
    caption: "@quasarframework",
    icon: "rss_feed",
    link: "https://twitter.quasar.dev",
  },
  {
    title: "Facebook",
    caption: "@QuasarFramework",
    icon: "public",
    link: "https://facebook.quasar.dev",
  },
  {
    title: "Quasar Awesome",
    caption: "Community Quasar projects",
    icon: "favorite",
    link: "https://awesome.quasar.dev",
  },
];

export default defineComponent({
  name: "MainLayout",

  components: {},
  data() {
    return {
      menuVisible: false,
      formType: "", // Hangi formun görüneceğini belirler
      firstName: "",
      lastName: "",
      email: "",
      password: "",
      message: "",
      messageColor: "red",
      dialogVisible: false,
      users: [],
    };
  },
  methods: {
    openMenu() {
      this.dialogVisible = true;
    },
    closeMenu() {
      this.dialogVisible = false;
    },
    // Giriş yap formunu açma
    switchToLogin() {
      this.formType = "login"; // Giriş formunu göster
      this.message = ""; // Mesajı sıfırlama
    },
    // Kayıt ol formunu açma
    switchToRegister() {
      this.formType = "register"; // Kayıt formunu göster
      this.message = ""; // Mesajı sıfırlama
    },
    // Geri dönmek için formType'ı sıfırlama
    switchToInitial() {
      this.formType = ""; // Başlangıçtaki butonları göster
      this.message = ""; // Mesajı sıfırlama
    },
    login() {
      const user = this.users.find(
        (user) => user.email === this.email && user.password === this.password
      );
      if (user) {
        this.messageColor = "green";
        this.message = "Giriş Başarılı!";
        this.$router.push("/"); // Giriş başarılı olduğunda yönlendirme
      } else {
        this.messageColor = "red";
        this.message = "E-posta veya şifre hatalı!";
      }
    },
    register() {
      // İsim ve soyisim kontrolü
      if (!this.firstName || !this.lastName || !this.email || !this.password) {
        this.messageColor = "red";
        this.message = "Lütfen tüm alanları doldurun!";
        return; // Boş alan varsa işlem yapılmaz
      }

      const existingUser = this.users.find((user) => user.email === this.email);
      if (existingUser) {
        this.messageColor = "red";
        this.message = "Kullanıcı zaten kayıtlı!";
      } else {
        this.users.push({
          firstName: this.firstName,
          lastName: this.lastName,
          email: this.email,
          password: this.password,
        });
        this.messageColor = "green";
        this.message = "Kayıt Başarılı!";
      }
    },
    loginCheck() {
    if (!this.email || !this.password) {
      this.messageColor = "red";
      this.message = "Lütfen tüm alanları doldurun!";
      return;
    }

    const user = this.users.find(
      (user) => user.email === this.email && user.password === this.password
    );

    if (user) {
      this.messageColor = "green";
      this.message = "Giriş Başarılı!";
      this.$router.push("/");
    } else {
      this.messageColor = "red";
      this.message = "E-posta veya şifre hatalı!";
    }
  },
  // Kayıt kontrol fonksiyonu
  registerCheck() {
    if (
      !this.firstName ||
      !this.lastName ||
      !this.email ||
      !this.password
    ) {
      this.messageColor = "red";
      this.message = "Lütfen tüm alanları doldurun!";
      return;
    }

    const existingUser = this.users.find(
      (user) => user.email === this.email
    );

    if (existingUser) {
      this.messageColor = "red";
      this.message = "Bu e-posta ile zaten kayıtlı bir kullanıcı var!";
    } else {
      this.users.push({
        firstName: this.firstName,
        lastName: this.lastName,
        email: this.email,
        password: this.password,
      });
      this.messageColor = "green";
      this.message = "Kayıt Başarılı!";
      this.formType = "";
    }
  },
  },
  setup() {
    const leftDrawerOpen = ref(false);
    return {
      essentialLinks: linksList,
      leftDrawerOpen,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
    };
  },
});
</script>

<style>
.logo-container {
  flex-grow: 1;
  display: flex;
  align-items: center;
  justify-content: center;
}

.logo-image {
  width: 200px; /* Adjust the width as needed */
  height: 30px; /* Adjust the height as needed */
}
.drawer {
  background-color: lightgray;
}
.header {
  background-color: black;
}
</style>
