<template>
  <div id="app">

    <div class="parrent-container" v-show="data">
      <input class="input-search" type="text" v-model="inputValue">
      <table class="table">
        <thead>
        <tr class="header">
          <th @click="sort('id')">Id</th>
          <th @click="sort('title')">Title</th>
          <th @click="sort('price')">Price</th>
          <th @click="sort('department')">Department</th>
          <th @click="sort('color')">Color</th>

        </tr>
        </thead>
        <tbody>
        <tr v-for="(item) in displayedData" :key="item.id">
          <th>{{item.id}}</th>
          <th>{{item.title}}</th>
          <th>{{item.price}}</th>
          <th>{{item.department}}</th>
          <th>{{item.color}}</th>
        </tr>
        </tbody>
      </table>
      <div>
       <div v-if="pages.length != 1"  class="pagination-container">
        <nav aria-label="Page pagination">
          <ul class="pagination">
            <li class="page-item">
              <button type="button" class="page-link" v-if="page != 1" @click="page--">Previous</button>
            </li>
            <li class="page-item">
              <button class="page-link" 
                v-for="pageNumber in pages.slice(page-1, page + 5)" 
                :key="pageNumber"
                @click="page = pageNumber">
                 {{pageNumber}}
              </button>
            </li>
             <li class="page-item">
              <button 
                type="button" 
                class="page-link" 
                v-if="page < pages.length" 
                @click="page++">
                  Next
                </button>
            </li>
          </ul>
        </nav>
      </div>
      </div>
    </div>
  
    <div v-show='!data' class="loading">Loading</div>
  </div>
</template>

<script>

export default {
  name: 'Table',
  
  data() {
    return {
      data: null,
      inputValue: '',
      page: 1,
      perPage: 20,
      pages: [],
      currentSort: 'Id',
      currentSortDir: 'asc',
    }
  },
  mounted(){
    fetch('./db.json').then(res => res.json()).then((data) => {
      this.data = data;
      this.priceToNumber()
      this.setPages();
      this.paginate(this.displayedData)

    })
  },
  computed: {
  
    computedData() {
      let searchString = this.inputValue
      if(searchString) {
       return this.data.filter((item) => item.title.includes(searchString))
      }
      return this.data
    },
    displayedData() {
      return this.paginate(this.sortedData)
    },
    sortedData() {
      let sorted = this.computedData;
      sorted.sort((a, b) => {
        let modifier = 1;
        switch (true) {
          case(this.currentSortDir === 'desc'): return modifier = -1;
          case(a[this.currentSort] < b[this.currentSort]): return -1 * modifier;
          case(a[this.currentSort] > b[this.currentSort]): return 1 * modifier;
          default: return 0;
        }     
      })
      return sorted;
    },
    numberOfPages() {
      return Math.ceil(this.computedData.length / this.perPage)
    }
  },
  methods: {
    adding() {
      this.$store.commit('increment')
    },
    priceToNumber() {
      this.data.forEach(element => {
        element.price = +parseFloat(element.price)
      });
    },
    sort(s) {
      if(s === this.currentSort) {
        this.currentSortDir = this.currentSortDir === 'asc' ? 'desc' : 'asc'
      }
      this.currentSort = s;
    },
   setPages() {
     this.pages = []
      for (let i = 1; i <= this.numberOfPages; i++) {
        this.pages.push(i);
      }
    },
    paginate(items) {
      let from = (this.page * this.perPage) - this.perPage;
      let to = this.page * this.perPage;
      return items.slice(from, to) 
    },
    sortBy(sortKey) {
      this.reverse = (this.sortKey == sortKey) ? !this.reverse : false;
      this.sortKey = sortKey;
    }
  },
  watch: {
    computedData() {
      this.setPages()
    },
    
  }
}
</script>

<style>
.input-search {
  border-radius: 45px;
  margin-bottom: 10px;
}
.parrent-container {
  display: grid;
  justify-content: center;
  align-items: center;
}
.header {
  cursor: pointer;
}

button.page-link {
	display: inline-block;
}
button.page-link {
    font-size: 20px;
    color: #29b3ed;
    font-weight: 500;
}
table {
  border: 2px solid black;
  border-collapse: collapse;
}
tbody tr:hover {
  background: rgb(144, 146, 145);
}
thead {
  background: rgb(53, 224, 90);
}
th {
  border: 1px solid;
}
.loading {
  background: blue;
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
