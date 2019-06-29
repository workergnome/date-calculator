<template>
  <section class="section">
    <div class="container">
      <h1 class="title">
        Date Testing Application
      </h1>
      <div class="columns">
        <div class="column is-half">
          <DateField debug="true" />
        </div>
        <div class="column"><p></p></div>
      </div>
    </div>
  </section>
</template>

<script>
import DateField from "./components/DateField.vue";

export default {
  name: "app",
  components: {
    DateField
  }
};
</script>

<style lang="scss"></style>
