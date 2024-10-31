<template>
  <footer id="site-footer">
    <section class="panel-top">
      <div class="footer-content">
        <h2 class="heading">Stay Informed</h2>
        <p class="subheading">
          <a target="_blank" href="https://ipfs.fyi/newsletter">Sign up</a> for the Ink Chain newsletter for
          the latest on releases, upcoming developments, community events, and more.
        </p>
      </div>

      <div class="footer-content">
        <h2 class="heading">Add Your Project</h2>
        <p class="subheading">
          Don’t see your project here? 
          <a href="https://airtable.com/shrjwvk9pAeAk0Ci7" target="_blank">Send it our way</a>.
        </p>
      </div>
    </section>
  </footer>
</template>

<script>
export default {
  name: 'ShipyardSiteFooter',
};
</script>

<style scoped lang="scss">
#site-footer {
  background-color: white; // Solid background for footer
  padding: 2rem 0;
  color: #333;
  box-shadow: 0px -2px 8px rgba(0, 0, 0, 0.1); // Shadow to lift footer from gradient background
  text-align: center;
}

.panel-top {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
}

.footer-content {
  width: 100%;
  max-width: 400px;
  margin: 1rem auto;
}

.heading {
  font-size: 1.5rem;
  color: #333;
  margin-bottom: 0.5rem;
}

.subheading {
  color: #666;
}

.subheading a {
  color: #FF5CAC; // Link color for contrast on white background
}
</style>
