<template>
  <div>
    <div class="active-form">
      <form
        class="active-form"
        @submit.prevent="submitLogin"
      >
        <TextInput
          v-model="email"
          placeholder="email"
          type="email"
          required
          name="email"
          class="item"
        />
        <TextInput
          v-model="password"
          placeholder="password"
          type="password"
          required
          class="item"
        />
        <BlockButton class="btn item">
          Login
        </BlockButton>
      </form>
    </div>
  </div>
</template>

<script>
import BlockButton from '@/components/BlockButton';
import TextInput from '@/components/TextInput';

import {
  loginManual,
  loginGoogle,
  isLoggedIn
} from '@/lib/cloudClient.js';

export default {
  components: {
    BlockButton,
    TextInput
  },
  data(){
    return {
      email: null,
      password: null
    };
  },
  methods: {
    async onGoogleSuccess(googleUser){
      try {
        await loginGoogle(
          googleUser.getAuthResponse().id_token,
          null
        );
        this.$store.commit('setIsLoggedIn', isLoggedIn());
        this.$router.push({name: 'Courses'});
      } catch (err){
        this.$notify({
          type: 'error',
          text: err
        });
      }
    },
    async submitLogin(){
      try {
        await loginManual(this.email, this.password);
        this.$store.commit('setIsLoggedIn', isLoggedIn());
        this.$router.push({name: 'Courses'});
      } catch (err){
        this.$notify({
          type: 'error',
          text: err
        });
      }
    }
  }
};
</script>

<style scoped lang="scss">
@import '@/styles/colors.scss';

.active-form {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  width: 100%;
  align-items: center;

  .item {
    margin-bottom: 2em;
    width: 100%;
  }

  .btn {
    margin-bottom: 2em;
    width: 50%;
    min-width: 250px;
  }
}
</style>
