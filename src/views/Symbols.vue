<template>
  <div>
    <div class="primary-heading-con">
      <div class="heading">
        <div class="title">Symbols/Tickers</div>
      </div>
    </div>

    <div class="page-content-con">
      <loading v-if="loading"></loading>
      <div v-else>
        <section class="section">
          <div class="columns">
            <div class="column">
              <h5 class="is-size-5">Search</h5>
              <input
                class="input"
                type="text"
                placeholder="Search Ticker or Name"
                v-model="textSearch"
              >
            </div>
            <div class="column">
              <h5 class="is-size-5">Sort</h5>
              <dropdown-selection :selections="sortOptions" v-on:input="sortCompaniesByName"></dropdown-selection>
            </div>
            <div class="column">
              <h5 class="is-size-5">Filter</h5>
              <dropdown-selection :selections="options" v-on:input="filterCompanies"></dropdown-selection>
            </div>
            <div class="column">
              <a class="button removeSymbolsBtn" v-on:click="isHidden = !isHidden">Remove Symbols</a>
            </div>
          </div>
        </section>
        <div class="coulumns">
          <div class="column" v-for="company in filteredCompanies" :key="company.symbol">
            <div class="company card">
              <header class="card-header">
                <a href="#">
                  <h5 class="card-header-title is-size-5">
                    <span v-if="!isHidden">{{company.symbol}} :&nbsp;</span>
                    <small class="is-size-6 companyName">{{company.companyName}}</small>
                  </h5>
                </a>
              </header>
              <div class="card-content">
                <div class="content">
                  <div>
                    Open
                    <money :value="company.open"></money>
                  </div>
                  <div>
                    Close
                    <money :value="company.close"></money>
                  </div>
                  <timestamp :value="company.openTime"></timestamp>-
                  <timestamp :value="company.closeTime"></timestamp>
                </div>
              </div>
              <footer class="card-footer">
                <a href="#" class="card-footer-item">Save</a>
                <a href="#" class="card-footer-item">Edit</a>
                <a href="#" class="card-footer-item">Delete</a>
              </footer>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import API from "../api/IEX";
import DropdownSelectionVue from "../elements/DropdownSelection.vue";
export default {
  name: "Symbols",
  data() {
    return {
      loading: true,
      isHidden: false,
      textSearch: "",
      sort: "",
      options: [
        { name: "Open", id: "open" },
        { name: "Close", id: "close" },
        { name: "Primary Exchange", id: "primaryExchange" }
      ],
      sortOptions: [{ name: "ASC", id: "asc" }, { name: "DESC", id: "desc" }],
      companies: []
    };
  },
  beforeMount() {
    API.getComputerHardwareCompanies()
      .then(response => {
        this.companies = response.data;
      })
      .finally(() => {
        this.loading = false;
      });
  },
  computed: {
    filteredCompanies: function() {
      const textSearch = this.textSearch;
      if (textSearch === "") {
        return this.companies;
      }
      return this.companies.filter(function(el) {
        return (
          el.companyName.toLowerCase().indexOf(textSearch.toLowerCase()) !==
            -1 ||
          el.symbol.toLowerCase().indexOf(textSearch.toLowerCase()) !== -1
        );
      });
    }
  },
  methods: {
    filterCompanies: function(el) {
      const companies = this.companies;
      switch (el.id) {
        case "open":
          companies.sort(function(a, b) {
            if (a.open === b.open) {
              return 0;
            }
            return a.open < b.open ? -1 : 1;
          });
          break;
        case "close":
          companies.sort(function(a, b) {
            if (a.close === b.close) {
              return 0;
            }
            return a.close < b.close ? -1 : 1;
          });
          break;
        case "primaryExchange":
          companies.sort(function(a, b) {
            if (a.companyName === b.companyName) {
              return 0;
            }
            return a.companyName < b.companyName ? -1 : 1;
          });
          break;
      }
    },
    sortCompaniesByName(el) {
      const companies = this.companies;
      switch (el.id) {
        case "asc":
          companies.sort(function(a, b) {
            if (a.companyName === b.companyName) {
              return 0;
            }
            return a.companyName < b.companyName ? -1 : 1;
          });
          break;
        case "desc":
          companies.sort(function(a, b) {
            if (a.companyName === b.companyName) {
              return 0;
            }
            return a.companyName > b.companyName ? -1 : 1;
          });
          break;
      }
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../assets/css/_theme";
.company {
  margin-bottom: 10px;
  padding-bottom: 10px;
  border-bottom: 1px solid $white-ter;
}
</style>
