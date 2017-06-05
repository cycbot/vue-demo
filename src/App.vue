<template>
  <div id="app">
    <div class="pane">
      <label for="input_city">城市</label>
      <input placeholder="请输入城市" id="input_city" type="text"
             v-model="inputCity" @focus="showList" @blur="showList">
      <div class="list" v-if="isShow">
        <ul>
          <li v-for="item in searchValue"><button @click="setValue(item)">{{item}}</button></li>
        </ul>
      </div>
      <label for="input_name">名称</label>
      <input id="input_name" type="text" placeholder="请输入名称" v-model="inputName">
      <button @click="search">搜索</button>
    </div>
    <div class="table">
      <table>
        <thead>
          <tr>
            <th><input type="checkbox" v-model="selectAll"></th>
            <th class="id">ID</th>
            <th class="name">名称</th>
            <th class="city">城市</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in items">
            <th><input type="checkbox" :value="item" v-model="selectItems"></th>
            <th class="id">{{item.id}}</th>
            <th class="name">{{item.name}}</th>
            <th class="city">{{item.city}}</th>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="info" v-if="selectItems.length != 0">
      <p>已选择{{selectItems.length}}项: <span>{{selectItems | checkedGroups}}</span></p>
    </div>
  </div>
</template>

<script>

export default {

  name: 'app',
  data () {
    return {
      items: [],
      inputName: '',
      inputCity: '',
      selectItems: [],
      tempSelect: [],
      data: [],
      isShow: false,
      list: ['北京','上海','成都','广州','深圳'],
      consult: ['北京','上海','成都','广州','深圳'],
    }
  },
  methods: {
    setValue (item) {
      this.inputCity = item
    },
    showList () {
      setTimeout(() => {
        this.isShow = !this.isShow
      }, 200)
    },
    search () {
      this.items = []
      this.selectItems = []
      this.data.forEach(item => {
        if (item.city == this.inputCity && item.name == this.inputName) {
          this.items.push(item)
        } else if ((item.city == this.inputCity || item.name == this.inputName) && (!this.inputCity || !this.inputName)) {
          this.items.push(item)
        } else if (!this.inputName && !this.inputCity) {
          this.items.push(item)
        }
      })
    }
  },
  filters: {
    checkedGroups (value) {
      let res = []
      value.forEach(item => {
        res.push(item.name)
      })
      return res.join(' ')
    }
  },
  computed: {
    searchValue () {
      this.consult = []
      if (this.inputCity === '') {
        return this.list
      }
      this.list.forEach(item => {
        if (this.inputCity[0] === item[0]) {
          this.consult.push(item)
        }
      })
      return this.consult
    },
    selectAll: {
      get () {
        let count = 0
        this.items.forEach(item => {
          this.selectItems.forEach(i => {
            if (i == item)count++
          })
        })
        if(this.items.length == 0)return false
        return (this.selectItems.length == this.items.length) || (count == this.items.length)
      },
      set (value) {
        if (value) {
          this.items.forEach(item => {
            let flag = true
            for (let i of this.selectItems) {
              if(i === item)flag = false
            }
            console.log(flag)
            if (flag) {
              this.selectItems.push(item)
            }
          })
        } else {
          this.items.forEach(item => {
            for (let i = 0; i < this.selectItems.length; i++) {
              if (this.selectItems[i] == item) {
                this.selectItems.splice(i,1)
              }
            }
          })
        }
      }
    }
  },
  beforeMount () {
    fetch('/api').then(res => {
      res.json().then(obj => {
        this.data = obj.data.data
        this.items = this.data
      })
    })
  }

}
</script>

<style lang="scss">
  .pane {
    position: relative;
    width: 780px;
    margin: 10px auto;
    padding: 20px 10px 20px 10px;
    box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 2px 20px 0 rgba(0, 0, 0, 0.19);
    border-radius: 5px;

    .list {
      position: absolute;
      width: 220px;
      height: 220px;
      overflow: auto;
      left: 50px;
      background: #fff;
      box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 2px 20px 0 rgba(0, 0, 0, 0.19);
      margin-top: 5px;

      z-index: 999;
      ul {
        padding-left: 5px;

        li {
          list-style: none;
          margin: 5px;

          button {
            width: 100%;
            font-size: 18px;
            color: #000;
            background-color: transparent;
          }
        }
      }


    }

    .fa-icon {
      position: relative;
      vertical-align: middle;
      right: 30px;
      z-index: 99;
    }

    input[type=text],select {
      width: 200px;
      border-radius: 5px;
      padding: 10px;
      border: 1px solid #d3d3d3;
    }

    button {
      width: 60px;
      color: white;
      background: #22A0E5;
      border: 0;
      border-radius: 5px;
      padding: 10px;
    }
  }
  .table {
    width: 800px;
    height: auto;
    margin: 0 auto;
    table {
      border-collapse: collapse;
      width: 800px;
      th {
        width: 20px;
        font-weight: 300;
        text-align: left;
        padding: 10px;
        border: 1px solid #d3d3d3;
      }

      thead th {
        font-weight: 500;
        border-bottom: 2px solid #d3d3d3;
      }

      .id {
        width: 160px;
      }

      .name {
        width: 360px;
      }

      .city {
        width: 260px;
      }

    }

  }

  .info {
    width: 800px;
    background: #d3d3d3;
    margin: 0 auto;
    border-radius: 5px;
    p {
      line-height: 20px;
      padding: 5px;
    }
  }
</style>
