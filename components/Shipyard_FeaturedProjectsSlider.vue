<template>
  <div class="project-card">
    <img :src="logo" :alt="title" class="project-logo" />
    <h2>{{ title }}</h2>
    <p>{{ description }}</p>
  </div>
</template>

<script>
export default {
  props: {
    title: String,
    logo: String,
    description: String,
  },
};
</script>

<style scoped lang="scss">
.project-card {
  background-color: white; // Solid background for each card
  border-radius: 8px;
  padding: 1rem;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1); // Light shadow for depth
  color: #333; // Dark text for readability on white background
  text-align: center;
}

.project-logo {
  width: 50px;
  height: 50px;
  margin-bottom: 1rem;
}
</style>
