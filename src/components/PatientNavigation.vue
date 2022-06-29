<script>
export default {
  data() {
    return {
      searchQuery: "",
    };
  },
  props: {
    data: Object,
  },
  emits: ['response'],
  methods: {
    formatDate(dateStr) {
      const options = {
        year: "numeric",
        month: "numeric",
        day: "numeric",
      };
      const rawDate = new Date(Date.parse(dateStr)).toLocaleDateString(
        "fr-FR",
        options
      );
      return rawDate;
    },
    onInput(e) {
      this.searchQuery = e.target.value;
      this.$emit('response', e.target.value)
    },
    submit(e) {
      this.$emit('response', e.target.value)
    }
  },
};
</script>

<template>
  <div class="PatientNavigation" v-if="data">
    <div class="PatientNavigation__top">
      <div class="PatientNavigation__top-info">
        <p class="patient-name">
          {{ data.first_name }} {{ data.last_name }}
          <span class="patient-id">#{{ data.id }}</span>
        </p>
        <p v-if="data.sex === 'F'">
          <span class="patient-data-type">Née le</span>
          {{ formatDate(data.birth_date) }}
          <img height="15px" src="../assets/female.png" /> Femme
        </p>
        <p v-else>
          <span class="patient-data-type">Né le</span>
          {{ formatDate(data.birth_date) }}
          <img height="15px" src="../assets/male.png" /> Homme
        </p>

        <p><span class="patient-data-type">IPP:</span> {{ data.ipp }}</p>
      </div>
      <div class="PatientNavigation__top-search">
        <div class="PatientNavigation__top-search-input-elements">
          <button v-on:click="submit">
            <img src="../assets/magnifier.png" />
          </button>
          <input type="text" @input="onInput" placeholder="Search..."/>
        </div>
      </div>
    </div>

    <nav class="PatientNavigation__bottom-nav">
      <a href="/">Documents</a>
      <a href="/">Biologie</a>
      <a href="/">Mouvements</a>
      <a href="/">Séjours</a>
      <a href="/">Phénotypes</a>
      <a href="/">Cohortes</a>
    </nav>
    <div class="PatientNavigation__separation"></div>
  </div>
</template>

<style>
.PatientNavigation {
  min-height: 150px;
  width: 100%;
  font-size: 13px;
  position: relative;

  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.PatientNavigation__top {
  display: grid;
  grid-template-columns: 220px auto 220px;
  gap: 10px;
  height: 80%;
  flex-grow: 1;
  text-align: center;
}
.PatientNavigation__top-info {
  grid-row: 1;
  grid-column: 1;
  display: flex;
  gap: 8px;
  padding-left: 20px;
  flex-direction: column;
  justify-content: center;
  text-align: start;
}
.PatientNavigation__top-search {
  grid-row: 1;
  grid-column: 2;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-grow: 1;
}
.PatientNavigation__top-search-input-elements {
  height: 25px;
  border-radius: var(--global-radius);
  border: 1px solid var(--color-border);
  box-shadow: rgba(0, 0, 0, 0.15) 0px 1px 3px 0px;
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 7px 10px;
}
.PatientNavigation__top-search img {
  height: 20px;
  opacity: 0.6;
  margin-top: 4px;
}
.PatientNavigation__top-search button {
  background: none;
  border: none;
}
.PatientNavigation__top-search input {
  width: 42vw;
  height: 100%;
  font-size: 16px;
  color: var(--font-color-grey);
  border: none;
  outline: none;
}
.PatientNavigation__top-info p {
  color: var(--font-color-grey);
  font-weight: 600;
  margin: 0;
}
.PatientNavigation__top-info .patient-name {
  color: #0c1625;
  font-size: 20px;
  font-weight: 600;
  margin-bottom: 2px;
}
.PatientNavigation__top-info .patient-name .patient-id {
  font-size: 14px;
}
.PatientNavigation__top-info .patient-data-type {
  font-weight: 500;
  color: var(--font-color-light-grey);
}
.PatientNavigation__bottom-nav {
  height: 20%;
  padding: 10px 0px;
}
.PatientNavigation__bottom-nav a {
  text-decoration: none;
  font-size: 16px;
  color: var(--font-color-grey);
  padding: 10px 15px;
}
.PatientNavigation__separation {
  position: absolute;
  bottom: 0;
  width: 100%;
  border-bottom: 2px solid var(--color-border);
}
</style>