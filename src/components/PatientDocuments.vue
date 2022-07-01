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
      ],
      sortKey: "date",
      sortOrders: {
        ...this.columns.reduce((obj, key) => {
          obj[key] = 1;
          return obj;
        }, {}),
        date: -1,
      },
      idFullDocument: null,
    };
  },
  computed: {
    filteredData() {
      const sortKey = this.sortKey;
      const searchQuery = this.searchQuery && this.searchQuery.toLowerCase();
      const order = this.sortOrders[sortKey] || 1;
      let data = this.patientData.documents.map((el) => {
        el["extract"] = this.convertToPlain(el.displayed_text).slice(0, 100);
        return el;
      });
      if (searchQuery) {
        const regEx = new RegExp(`(${searchQuery})`, "ig");
        data = data
          .filter((document) => {
            return (
              document.displayed_text.toLowerCase().indexOf(searchQuery) > -1
            );
          })
          .map((document) => {
            document["displayed_text-highlighted"] =
              document.displayed_text.replaceAll(
                regEx,
                "<span class='highlight-text'>$1</span>"
              );
            document["extract-highlighted"] = document.extract.replaceAll(
              regEx,
              "<span class='highlight-text'>$1</span>"
            );
            return document;
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
    showFullDocument(id) {
      if (this.idFullDocument === id) {
        this.idFullDocument = null;
        return;
      }
      this.idFullDocument = id;
    },
    convertToPlain(html) {
      let divElement = document.createElement("div");
      divElement.innerHTML = html;
      return divElement.innerText || "";
    },
    formatDate(dateStr) {
      const options = {
        month: "short",
        day: "numeric",
      };
      const rawDate = new Date(Date.parse(dateStr)).toLocaleDateString(
        "fr-FR",
        options
      );
      return rawDate;
    },
    areSameYears(date1, date2) {
      const year1 = new Date(date1).getFullYear();
      const year2 = new Date(date2).getFullYear();
      return year1 === year2;
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
      <col style="max-width: auto" />
      <col style="width: 14%" />
      <col style="width: auto" />
      <col style="width: auto" />
      <col style="width: 2%" />
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
        <template v-for="(entry, index) in filteredData">
          <tr
            :key="entry.id"
            :class="[
              idFullDocument === entry.id && 'background-darker',
              index > 0 &&
                !areSameYears(filteredData[index - 1].date, entry.date) &&
                'top-border',
            ]"
          >
            <td v-for="key in columns" :key="key">
              <div v-if="key === 'date'" class="PatientDocuments__document">
                <span
                  v-if="
                    index === 0 ||
                    !areSameYears(filteredData[index - 1].date, entry.date)
                  "
                  class="PatientDocuments__document-year"
                >
                  {{ new Date(entry["date"]).getFullYear() }}
                </span>
                <span class="PatientDocuments__document-day-month">{{
                  formatDate(entry["date"])
                }}</span>
              </div>

              <div v-else-if="key !== 'aperçu'">
                {{ entry[key] }}
              </div>

              <div v-else-if="idFullDocument !== entry.id">
                <h2>{{ entry["title"] }}</h2>
                <p
                  v-if="searchQuery !== ''"
                  class="PatientDocuments__document-extract"
                  v-html="entry['extract-highlighted']"
                ></p>
                <p
                  v-else
                  class="PatientDocuments__document-extract"
                  v-html="entry['extract']"
                ></p>
                ...
              </div>
            </td>
            <td class="document-button">
              <button @click="showFullDocument(entry.id)">
                <span
                  class="arrow"
                  :class="idFullDocument === entry.id ? 'dsc' : 'right'"
                >
                </span>
              </button>
            </td>
          </tr>

          <tr
            class="full-document-row"
            :key="entry.title"
            v-if="idFullDocument == entry.id"
          >
            <td colspan="5">
              <h2>{{ entry["title"] }}</h2>
              <p
                v-if="searchQuery !== ''"
                class="PatientDocuments__document-content"
                v-html="entry['displayed_text-highlighted']"
              ></p>
              <p
                v-else
                class="PatientDocuments__document-content"
                v-html="entry['displayed_text']"
              ></p>
            </td>
          </tr>
        </template>
      </tbody>
    </table>

    <p v-else>No matches found.</p>
  </div>
</template>

<style>
.highlight-text {
  background-color: #ffff00;
  display: inline;
}
.PatientDocuments {
  background-color: var(--background-color);
  flex-grow: 1;
  padding: 0 20px 20px 20px;
}
.PatientDocuments__document-year {
  font-weight: 700;
}
.PatientDocuments__document-day-month {
  padding: 0 10px;
  float: right;
}
.PatientDocuments .PatientDocuments__document-extract {
  display: inline;
}

.full-document-row .PatientDocuments__document-content {
  display: block;
  border-top: 1px solid var(--color-border);
  margin-top: 15px;
  padding-top: 15px;
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
h2 {
  margin: 0;
  font-size: 18px;
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
  width: 115px;
}
th:last-child,
td:last-child {
  padding-right: 20px;
}
th,
td {
  min-width: 100px;
  padding-top: 10px;
  padding-bottom: 10px;
  padding-right: 10px;
}
th {
  cursor: pointer;
}

table .full-document-row {
  border-bottom: 1px solid var(--color-border);
}
table .top-border {
  border-top: 1px solid var(--color-border);
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
  border-bottom: 5px solid rgb(59, 58, 58);
}

.arrow.dsc {
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-top: 5px solid rgb(77, 75, 75);
}
.arrow.right {
  border-left: 5px solid rgb(59, 58, 58);
  border-bottom: 4px solid transparent;
  border-top: 4px solid transparent;
}
.document-button {
  margin: 0;
  text-align: right;
}
.document-button button {
  border: none;
  background: none;
}
.document-button button:hover {
  cursor: pointer;
}
.background-darker {
  background-color: #f3f5f9;
  border-bottom: none;
}
</style>