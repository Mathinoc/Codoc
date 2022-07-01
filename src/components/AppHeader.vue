<script>
export default {

};
</script>

<template>
  <div class="AppHeader">
    <h1 class="AppHeader__title">CODOC</h1>
    <nav>
      <p>Recherche</p>
      <p>Cohortes</p>
      <p>Export</p>
      <p>Admin</p>
    </nav>
  </div>
</template>

<style>
.AppHeader {
  background-color: rgb(255, 255, 255);
  height: var(--header-height);
  position: fixed;
  width: 100%;
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 10px;
  padding-left: 20px;
  color: #223c75;
  font-size: 16px;
  box-shadow: rgba(0, 0, 0, 0.15) 0px 2px 4px 0px;
}
.AppHeader__title {
  margin: 0;
  font-size: 20px;
  padding-right: 10px;
  border-right: 1px solid #223c75;
}
.AppHeader nav {
  display: flex;
  padding: 0 10px;
}
.AppHeader nav p{
  padding: 0 10px;
}

</style>