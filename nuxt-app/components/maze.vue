<template>
  <div class="position-absolute top-100 start-50 translate-middle d-flex">
    <div class="m-5">
      <h2>arr</h2>
      <div v-for="item in number" class="d-flex">

        <div v-for="item2 in number">
          <div v-if="arr[item-1][item2-1] === 0" class="bluebox m-2">

          </div>

          <div v-if="arr[item-1][item2-1] === 1" class="redbox m-2">

          </div>
          <div v-if="arr[item-1][item2-1] === 2" class="blackbox m-2">

          </div>
          <div v-if="arr[item-1][item2-1] === 3" class="greenbox m-2">

          </div>
        </div>

      </div>
    </div>

<!--    <div class="m-5">
      <h2>temp</h2>
      <div v-for="i in number" class="d-flex">

        <div v-for="j in number">
          <div v-if="tepmarr[i-1][j-1] !== 1" class="bluebox m-2">

          </div>

          <div v-if="tepmarr[i-1][j-1] === 1" class="redbox m-2">

          </div>
          &lt;!&ndash;          <div v-if="tepmarr[i-1][j-1] === 2" class="blackbox m-2">

                    </div>
                    <div v-if="tepmarr[i-1][j-1] === 3" class="greenbox m-2">

                    </div>
                  &ndash;&gt;
        </div>

      </div>
    </div>-->



    <button class="btn btn-outline-info" @click="opensettingmenu()">change setting</button>
        <div v-if="settingmenu === true" class="">
      <!--      <button class="btn btn-success m-3" @click="findway">find way</button>-->
      <button class="btn btn-success m-3" @click="findway">Find way</button>
      <button class="btn btn-success m-3" @click="opencreatemenu()">choose size of maze</button>
      <div class="m-3" v-if="createmaze === true">
        <input type="number" v-model="number" @change="fixparams()">

      </div>
      <button class="btn btn-success m-3" @click="openwaymenu()">choose way of maze</button>
      <div class="m-3" v-if="chooseway === true">
        <div>
          <h5>start point</h5>
          <input type="number" class="m-2" v-model="startr">
          <input type="number" class="m-2" v-model="startc">
        </div>
        <div>
          <h5>end point</h5>
          <input type="number" class="m-2" v-model="endr">
          <input type="number" class="m-2" v-model="endc">
        </div>


      </div>
      <button class="btn btn-success m-3" @click="creatematris">creatematris</button>


      <input type="file" class="m-3" @change="readmatris" id="matrisfile">

      <p class="alert alert-danger" v-if="text != null">{{ text }}</p>
      <p class="alert alert-danger" v-if="errorr != null">{{ errorr }}</p>
      <p class="alert alert-info m-3">Total lenght of way is : {{ count }}</p>
    </div>










  </div>
</template>

<script>




export default {
  data() {
    return {
      settingmenu : false,
      number: 10,
      endr: null,
      endc: null,
      startr: 0,
      startc: 0,
      chooseway: false,
      arr: [],
      tepmarr: [],
      way: [{'row': this.startr, 'column': this.startc, 'way': 0}],
      count: 0,
      foundways: [{
        'row': 1,
        'column': 0,
        'way': 1
      }, {
        'row': 1,
        'column': 1,
        'way': 2

      }, {
        'row': 0,
        'column': 1,
        'way': 3
      }, {
        'row': -1,
        'column': 1,
        'way': 4
      }, {
        'row': -1,
        'column': 0,
        'way': 5
      }, {
        'row': -1,
        'column': -1,
        'way': 6
      }, {
        'row': 0,
        'column': -1,
        'way': 7
      }, {
        'row': 1,
        'column': -1,
        'way': 8
      },
      ],
      text: null,
      errorr: null,
      foundmatriscount: 0,
      mat: null,
      createmaze: false
    }
  },
  methods: {
    opensettingmenu(){
      this.settingmenu = !this.settingmenu

    },
    fixparams(){
      this.endc = this.number - 1
      this.endr = this.number - 1
      this.startr = 0
      this.startc = 0
    },
    openwaymenu() {
      this.chooseway = !this.chooseway
    },
    opencreatemenu() {
      this.createmaze = !this.createmaze
    },
    create2darray() {
      this.createmaze = false
      this.chooseway = false


      for (let i = 0; i < this.number; i++) {
        this.arr[i] = [];
      }
      if (this.mat !== null) {

        let k = 1

        for (let i = 0; i < this.number; i++) {
          let z = 0
          for (let j = 0; j < this.number; j++) {

            this.arr[i][j] = Number(this.mat[k][z])

            z += 2
          }
          k++

        }
      } else {
        for (let i = 0; i < this.number; i++) {
          for (let j = 0; j < this.number; j++) {
            let arrvalue = Math.floor(Math.random() * 2)
            this.arr[i][j] = arrvalue
          }
        }
        this.arr[this.startr][this.startc] = 0
        this.arr[this.endr][this.endc] = 0
      }
      this.tepmarr = this.arr
      this.mat = null
    },
    readmatris() {
      console.log('read matris')
      let fr = new FileReader()
      let file = document.getElementById('matrisfile')

      fr.readAsText(file.files[0])
      let vue = this
      fr.onload = function () {
        vue.mat = fr.result
        vue.mat = vue.mat.split(/\r\n|\n/)
        vue.number = Math.floor(vue.mat[0])


        vue.create2darray()

      }


    },


    creatematris() {
      this.number = Math.round(this.number)
      this.way = [{'row': this.startr, 'column': this.startc, 'way': 0}]
      this.count = 0
      this.text = null
      this.errorr = null
      this.foundmatriscount = 0

      this.create2darray(this.number)
      this.tepmarr = [...this.arr]
      if (this.findway() || this.foundmatriscount > 5000) {
        this.way = [{'row': 0, 'column': 0, 'way': 0}]

        this.text = null
        this.errorr = null
        this.foundmatriscount = 0

      } else {
        this.foundmatriscount++
        this.count = 0
        this.creatematris()
      }


    },


    findway() {
      this.count = 0
      let w = 0

      while (true) {
        let foundway = false
        this.count++
        let l = 0
        if (this.way.length !== 0) {
          l = this.way.length - 1
        } else {
          break
        }


        let i = this.way[l].row
        let j = this.way[l].column


        let fway = []

        if (this.count > 10000) {
          this.text = 'there is no way'
          break
        }

        for (let item of this.foundways) {
          if (item.way !== w) {
            fway.push(item)
          }
        }
        w = 0
        for (let item of fway) {
          try {
            if (this.arr[item.row + i][item.column + j] === 0) {
              this.way.push({
                'row': item.row + i,
                'column': item.column + j,
                'way': item.way
              })
              this.arr[item.row + i][item.column + j] = 2

              foundway = true
              break
            }
          } catch (e) {
            continue
          }
        }
        if (this.arr[this.endr][this.endc] === 2) {
          this.text = 'way founded!'
          return true
          break

        }
        if (foundway) {
          continue
        } else {
          this.arr[i][j] = 3

        }


        w = this.way.pop().way

      }



      this.arr[0][0] = 2
      this.count = 0
      for (let i = 0; i < this.number; i++) {
        for (let j = 0; j < this.number; j++) {
          if (this.arr[i][j] === 2) {
            this.count++
          }
        }
      }

      /*     for (let i = 0; i < this.arr.length; i++) {
             for (let j = 0; j < this.arr.length; j++) {
               if (this.arr[i][j] === 3) {
                 this.arr[i][j] = 0
               }
             }

           }*/
    }
  },
  beforeMount() {
    this.endr = this.number- 1
    this.endc = this.number- 1
    this.creatematris()
  }
}


</script>

<style scoped>
.btn-outline-info{
  height: fit-content;
}

.btn-outline-info:hover {
  color: white;
}
.redbox {
  padding: 15px;
  background: red;
}

.bluebox {
  padding: 15px;
  background: blue;
}

.blackbox {
  padding: 15px;
  background: black;
}

.greenbox {
  padding: 15px;
  background: green;
}
</style>