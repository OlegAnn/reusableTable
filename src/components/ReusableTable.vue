<template>
  <div class="wrapper">
    <div class="wrapper_tools">
      <article class="column_left">
        Filter
        <input 
          placeholder="start typing to filter" 
          v-model="query"
        >
        <button :disabled='!query' @click="appendFilter()">add filter</button>
        select type of filter
        <select v-model="filteredProperty">
          <option 
            v-for="(item, index) in titles"
            :key="index"
            :value="index"
          >
            {{item}}
          </option>
        </select>
        <div v-if="activeFilters.length">
          <ul>
            <li>Filters in use:</li>
          </ul>
          <ul v-for="(filter, index) in activeFilters" :key="index">
            <li>{{ filter.name }}:</li>
            <li>{{ filter.value }}</li>
            <li style="padding-left: 10px;">
              <a href="#" @click="removeItemInFilters(index)">
                remove
              </a>
            </li>
          </ul>
        </div>
      </article>
      <article class="pagination">
        <div class="pagination-wrapper-numbers">
          <div v-for="(page ,index) in pageCount" :key="index" class="pagination-numbers">
          <span @click="goToPage(index)" :class="{activePage: index === pageNumber}">{{page}}</span>
        </div>
        </div>
      </article>
    </div>
    <div class="wrapper_table">
      <div class="wrapper_title">
        <ul class="row">
          <li 
            class="column" 
            v-for="(item, index) in titles" 
            :key="index"
            :class="{ active: sortKey == index }"
            @click="sortBy(index)"
          >
            {{item}}
            <span class="arrow" :class="sortOrders[index] > 0 ? 'asc' : 'desc'"></span>
          </li>
        </ul>
      </div>
      <ul
        v-for="(item,index) in paginatedData" 
        :key="index"
      >
        <slot 
          :item='item'
        >
        </slot>
      </ul>

    </div>
  </div>
</template>

<script>
export default {
  name: 'ReusableTable',
  props: {
    items: {
      type: Array,
      required: true
    },
    titles: Object
  },
  data: () => ({
    activeFilters: [],
    sortOrders: {},
    sortKey: '',
    query: '',
    filteredProperty: '',
    pageNumber: 0,
    size: 10
  }),
  created () {
    this.sortByOrders()
  },
  beforeMount () {
    this.enterInitialFilter()
  },
  methods: {
    enterInitialFilter() {
      this.filteredProperty = Object.keys(this.titles)[0]
    },
    sortByOrders () {
      let sortOrders = {}
      Object.keys(this.titles).forEach(key => {
        sortOrders[key] = 1
      })
      this.sortOrders = sortOrders
    },
    sortBy (key) {
      this.sortKey = key
      this.sortOrders[key] = this.sortOrders[key] * -1
    },
    resetPageNumber () {
      this.pageNumber = 0
    },
    appendFilter() {
      // Creating activeFilters without duplicates by name
      this.resetPageNumber()
      if (!this.activeFilters.map(el => el.name == this.filteredProperty ? el.name : false).includes(this.filteredProperty))
        this.activeFilters.push({
          name: this.filteredProperty,
          value: this.query.toLowerCase()
        })
        this.query = ''
    },
    removeItemInFilters (index) {
      this.activeFilters.splice(index, 1)
    },
    goToPage(num) {
      this.pageNumber = num
    },
  },
  computed: {
    paginatedData() {
      const start = this.pageNumber * this.size,
        end = start + this.size;
      let paginated = this.filtered.slice(start, end);
      return paginated
    },
    pageCount() {
      let l = this.filtered.length,
      s = this.size;
      return Math.ceil(l/s);
    },
    filtered () {
      let filtered = this.items
      let sortKey = this.sortKey
      let order = this.sortOrders[sortKey] || 1
      // if use filter
      if (this.appendFilter) {
        this.activeFilters.forEach(filter => {
          filtered = filtered.filter(record => {
            return filter.name === 'name'
              ? new RegExp(filter.value, 'i').test(record[filter.name].toLowerCase())
              : record[filter.name].toLowerCase() == filter.value
          })
        })
      }
      // if use sorting
      if (sortKey) {
        filtered = filtered.slice().sort(function (a,b) {
          // if the string has a number or has the 'BBY' we bring it to number for more accurate sorting
          a = new RegExp('BBY').test(a[sortKey]) 
            ? parseFloat(a[sortKey]) : new RegExp(/^\d+(?:[\.,]\d+)?$/).test(a[sortKey]) 
            ? parseFloat(a[sortKey]) : a[sortKey]
          b = new RegExp('BBY').test(b[sortKey]) 
            ? parseFloat(b[sortKey]) : new RegExp(/^\d+(?:[\.,]\d+)?$/).test(b[sortKey]) 
            ? parseFloat(b[sortKey]) : b[sortKey]
          // for exact sorting if one element is number and the second line we bring both to number
          typeof a == 'number' && typeof b == 'string' ? b = ~~b : b
          typeof b == 'number' && typeof a == 'string' ? a = ~~a : a

          return (a === b ? 0 : a > b ? 1 : -1) * order
        })
      }      
      return filtered
    }
  }
}
</script>
<style lang="scss" scoped>
  .wrapper{
    .wrapper_tools{
      display: flex;
      justify-content: center;
      flex-direction: column;
      align-items: center;
      .pagination{
        &-wrapper-numbers{
          display: flex;
          justify-content: center;
          margin: 20px 0;
          .pagination-numbers{
            cursor: pointer;
            margin: 0 10px;
            span{
              padding: 10px;
              border: 1px solid black
            }
            .activePage{
              background: rgb(209, 151, 151);
            }
          }
        }
      }
    }
    .wrapper_table{
      display: flex;
      justify-content: center;
      flex-direction: column;
      align-items: center;
      .wrapper_title{
        .row{
          padding: 0;
          display: flex;
          flex-wrap: nowrap;
          .column{
            display: inline-block;
            background-color: #302821;
            color: rgba(255,255,255,0.66);
            min-width: 133px;
            padding: 10px 20px;
            cursor: pointer;
            .arrow{
              display: inline-block;
              vertical-align: middle;
              width: 0;
              height: 0;
              margin-left: 5px;
              opacity: 0.36;
              transition: .5s;
            }
            .asc{
              border: 7px solid transparent;
              border-top: 7px solid white;         
            }
            .desc{
              border: 7px solid transparent;
              border-bottom: 7px solid white;                 
            }
          }
          .active{
            color: white;
            .arrow{
              opacity: 1;
            }
          }
        }
      }
      .wrapper_body{
        display: flex;
        justify-content: center;
        flex-direction: column;
        align-items: center;
        .row{
          display: flex;
        }
      }
      ul {
        list-style-type: none;
        padding: 0;
        display: flex;
      }
    }
  }
</style>