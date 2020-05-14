<template>
  <v-container>
    <userForm />
  </v-container>
</template>

<script>
import userForm from "./../components/userForm";
export default {
  name: "homePage",
  components: {
    userForm
  }
};
</script>
