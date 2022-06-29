<script>
export default {
  props: {
    patientData: Object,
    searchQuery: String,
    columns: Array,
  },
  data() {
    return {
      documentKeys: [
        "document_date",
        "document_type",
        "document_origin_code",
        "title",
        // "displayed_text"
      ],
      sortKey: "date",
      sortOrders: {
        ...this.columns.reduce((o, key) => ((o[key] = 1), o), {}),
        date: -1,
      },
    };
  },
  computed: {
    filteredData() {
      const sortKey = this.sortKey;
      const filterKey = this.searchQuery && this.searchQuery.toLowerCase();
      const order = this.sortOrders[sortKey] || 1;
      let data = this.patientData.documents;
      if (filterKey) {
        data = data.filter((row) => {
          return Object.keys(row).some((key) => {
            return String(row[key]).toLowerCase().indexOf(filterKey) > -1;
          });
        });
      }
      if (sortKey) {
        if (sortKey !== "aperçu") {
          data = data.slice().sort((a, b) => {
            a = a[sortKey];
            b = b[sortKey];
            return (a === b ? 0 : a > b ? 1 : -1) * order;
          });
        } else {
          data = data.slice().sort((a, b) => {
            a = a["title"];
            b = b["title"];
            return (a === b ? 0 : a > b ? 1 : -1) * order;
          });
        }
      }
      return data;
    },
  },
  methods: {
    sortBy(key) {
      this.sortKey = key;
      this.sortOrders[key] = this.sortOrders[key] * -1;
    },
    capitalize(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
  },
};
</script>

<template>
  <div class="PatientDocuments" v-if="patientData">
    <p v-if="patientData.documents.length > 1">
      <img src="../assets/document.png" />
      {{ patientData.documents.length }} documents trouvés
    </p>
    <p v-else>
      <img src="../assets/document.png" />
      {{ patientData.documents.length }} document trouvé
    </p>
    <table v-if="filteredData.length">
      <col style="width: auto" />
      <col style="width: 14%" />
      <col style="width: auto" />
      <col style="width: auto" />
      <col style="width: 20px" />
      <thead>
        <tr>
          <th
            v-for="key in this.columns"
            :key="key"
            @click="sortBy(key)"
            :class="{ active: sortKey == key }"
          >
            {{ capitalize(key) }}
            <span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'">
            </span>
          </th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="entry in filteredData" :key="entry.title">
          <td v-for="key in columns" :key="key">
            <div v-if="key !== 'aperçu'">
              {{ entry[key] }}
            </div>
            <div v-else>
              <p class="document-title-button">{{ entry["title"] }}<button><span class="arrow" :class="sortOrders[key] > 0 ? 'right' : 'dsc'">
            </span></button></p>
              <p>{{ entry["extract"] }}...</p>
              <!-- <p class="PatientDocuments__document-content" v-html="entry['displayed_text']"></p> -->
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <p v-else>No matches found.</p>
  </div>
</template>

<style>
.PatientDocuments {
  background-color: var(--background-color);
  flex-grow: 1;
  padding: 0 20px;
}
.PatientDocuments__document-content {
  display: flex;
  flex-direction: column;
  align-items: left;
  text-align: left;
}
.PatientDocuments p {
  display: flex;
  align-items: center;
  gap: 5px;
}
.PatientDocuments img {
  padding-bottom: 2px;
  height: 18px;
}
table {
  border-collapse: collapse;
  border-radius: var(--global-radius);
  box-shadow: 0 0 0 1px var(--color-border);

  background-color: white;
  text-align: left;
  width: 100%;
  overflow: scroll;
}
thead {
  border-radius: var(--global-radius);
  border-bottom: 1px solid var(--color-border);
}
tr {
  vertical-align: top;
}
th:first-child,
td:first-child {
  padding-left: 10px;
}
th,
td {
  min-width: 100px;
  padding-top: 10px;
  padding-bottom: 10px;
}
th {
  cursor: pointer;
}

table {
}
th.active .arrow {
  opacity: 1;
}
.arrow {
  display: inline-block;
  vertical-align: middle;
  width: 0;
  height: 0;
  margin-left: 5px;
  opacity: 0.66;
}

.arrow.asc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-bottom: 4px solid rgb(59, 58, 58);
}

.arrow.dsc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-top: 4px solid rgb(77, 75, 75);
}
.arrow.right{
  border-left: 4px solid rgb(59, 58, 58);
  border-bottom: 4px solid transparent;
  border-top: 4px solid transparent;
}
.document-title-button{
  margin: 0;
  display:flex;
  justify-content: space-between;
  text-align: right;
}
</style>