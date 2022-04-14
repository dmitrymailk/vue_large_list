<template>
  <div class="card">
    <h3>{{ title }}</h3>
    <p>{{ text }}</p>
  </div>
</template>

<script>
export default {
  props: ["text", "title", "mountedCallback"],

  mounted() {
    // console.log("mounted", this.$el);

    const elementHeight = this.$el.parentNode.offsetHeight + 10;
    this.$props.mountedCallback(elementHeight);
  },
};
</script>
