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
          const textDocuments = patient.documents.map((el) => {
            const text = this.cleanUpHtml(el.displayed_text);
            el.displayed_text = text;
            return el;
          });
          patient.documents = textDocuments;
          return patient;
        })
        .then((patient) => {
          const documents = patient.documents.map((el, index) => {
            el["date"] = el.document_date //this.formatDate(el.document_date);
            el["type"] = el.document_type;
            el["origine"] = el.document_origin_code;
            el.id = index;
            delete el.document_date;
            delete el.document_type;
            delete el.document_origin_code;
            return el;
          });
          patient.documents = documents;
          return patient;
        })
        .then((patient) => (this.patientData = patient))
        .catch((error) => console.log(error));
    },
    convertToPlain(html) {
      let tempDivElement = document.createElement("div");
      tempDivElement.innerHTML = html;
      return tempDivElement.textContent || tempDivElement.innerText || "";
    },
    cleanUpHtml(html) {
      return html.replace(/(\\n)*(\\t)*/g, "");
    },
    formatDate(dateStr) {
      const options = {
        year: "numeric",
        month: "short",
        day: "numeric",
      };
      const rawDate = new Date(Date.parse(dateStr)).toLocaleDateString(
        "fr-FR",
        options
      );
      return rawDate;
    },
  },
};
</script>

<template>
  <div class="PatientPortal">
    <PatientNavigation
      :data="patientData"
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