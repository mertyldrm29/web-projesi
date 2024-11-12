<template>
  <q-page class="center-contentgiris">
    <q-card class="login-card">
      <q-card-section>
        <h5 class="textgiris">Giriş Yap</h5>
        <q-form @submit="login">
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
            <q-btn type="submit" color="primary" label="Giriş Yap" dense />
            <q-btn @click="register" color="secondary" label="Kayıt Ol" dense />
          </q-card-actions>
        </q-form>
        <p v-if="message" :style="{ color: messageColor }">{{ message }}</p>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script>
export default {
  data() {
    return {
      email: "",
      password: "",
      users: [], // Kayıtlı kullanıcıları tutacak dizi
      message: "", // Durum mesajı
      messageColor: "red", // Mesajın rengi (hata mesajı için kırmızı)
    };
  },
  methods: {
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
      const existingUser = this.users.find((user) => user.email === this.email);
      if (existingUser) {
        this.messageColor = "red";
        this.message = "Kullanıcı zaten kayıtlı!";
      } else {
        this.users.push({ email: this.email, password: this.password });
        this.messageColor = "green";
        this.message = "Kayıt Başarılı!";
      }
    },
  },
};
</script>

<style>
.center-contentgiris {
  margin-left: 500px;
  height: 10vh;
}

.login-card {
  margin-left: -100px;
  margin-top: 100px;
  max-width: 300px;
  padding: 30px;
}

.textgiris {
  margin-left: 50px;
  font-weight: bold;
}

p {
  text-align: center;
  font-size: 14px;
  font-weight: bold;
}
</style>
