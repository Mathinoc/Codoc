<script>
import PatientNavigation from "./PatientNavigation.vue";
import PatientDocuments from "./PatientDocuments.vue";
export default {
  components: {
    PatientNavigation,
    PatientDocuments,
  },
  data() {
    return {
      searchQuery: "",
      patientData: null,
      columns: ["date", "type", "origine", "aperçu"],
    };
  },
  created() {
    this.fetchPatient();
  },
  methods: {
    async fetchPatient() {
      fetch("./data.json", {
        headers: {
          "Content-type": "application/json",
          Accept: "application/json",
        },
      })
        .then((data) => data.json())
        .then((patient) => {
          const textDocuments = patient.documents.map((el, index) => {
            el["date"] = el.document_date;
            el["type"] = el.document_type;
            el["origine"] = el.document_origin_code;
            el.id = index;
            el.displayed_text = this.cleanUpHtml(el.displayed_text);
            delete el.document_date;
            delete el.document_type;
            delete el.document_origin_code;

            return el;
          });
          patient.documents = textDocuments;
          return patient;
        })
        .then((patient) => (this.patientData = patient))
        .catch((error) => console.error(error));
    },
    cleanUpHtml(html) {
      return html.replace(/(\\n)*(\\t)*/g, "");
    },
  },
};
</script>

<template>
  <div class="PatientPortal">
    <PatientNavigation
      :patientData="patientData"
      @response="(search) => (searchQuery = search)"
    />
    <PatientDocuments
      :patientData="patientData"
      :searchQuery="searchQuery"
      :columns="columns"
    />
  </div>
</template>

<style>
.PatientPortal {
  display: flex;
  flex-direction: column;
  min-height: calc(100vh - var(--header-height));
}
</style>