<template>
  <div class="czb__table">
    <div class="czb__table__box" ref="box">    
      <div class="czb__table__wrap" :style="{'width': wrapWidth}">
        <div class="czb__table__head">
          <div class="czb__table__head__item czb__table__head__check" v-if="hasCheck">
            <i class="czb__table__checkbox" :class="{'is-choosed': checkedAll}" @click="checkAll"></i>
          </div>
          <div class="czb__table__head__item" v-for="col in columns" :key="col.key" :style="{'width': col.width+'px'}">{{col.title}}</div>
          <div class="czb__table__head__item" v-if="handle">操作</div>
        </div>
        <div class="czb__table__content">
          <div class="czb__table__content__row" v-for="(row, index) in tableData" :key="row.id">
            <div class="czb__table__content__col czb__table__content__check" v-if="hasCheck">
              <i class="czb__table__checkbox" :class="{'is-choosed': row.isChoosed}" @click="checkboxClick(row)"></i>
            </div>
            <!-- <div class="czb__table__content__col" v-for="col in columns" :key="col.key" v-html="(col.render && col.render(row[col.key], row, index)) || row[col.key]" :style="{'width': col.width+'px'}"></div> -->
            <div class="czb__table__content__col" v-for="col in columns" :key="col.key" v-html="(col.render && col.render(row[col.key], row, index)) || row[col.key]" :style="{'width': col.width+'px'}"></div>
            <div class="czb__table__content__col" v-if="handle">
              <a href="javascript:void(0)" v-for="(obj, btnIndex) in handle" :key="btnIndex" @click="handleClick({btnIndex, row, index})">{{obj}}</a>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- <div class="czb__table__pagination">
      <div class="czb__table__pagination__wrap">
        <div class="czb__table__pagination__previous">previous</div>
        <div class="czb__table__pagination__item">1</div>
        <div class="czb__table__pagination__item">2</div>
        <div class="czb__table__pagination__item">3</div>
        <div class="czb__table__pagination__item">4</div>
        <div class="czb__table__pagination__item">5</div>
        <div class="czb__table__pagination__item">6</div>
        <div class="czb__table__pagination__next">next</div>
      </div>
    </div> -->
  </div>
</template>
<script>
export default{
  name: 'czb-table',
  data () {
    return {
      checkedAll: false,
      wrapWidth: 'auto'
    }
  },
  props: {
    columns: {
      type: Array,
      required: true
    },
    tableData: {
      type: Array,
      required: true
    },
    hasCheck: {
      type: Boolean
    },
    pagination: {
      type: Object
    },
    handle: {}
  },
  computed: {
    choosedData () {
      return this.tableData.filter(obj => {
        return obj.isChoosed
      })
    }
  },
  methods: {
    checkboxClick (item) {
      this.$set(item, 'isChoosed', !item.isChoosed)
      this.$emit('chooseItem', this.choosedData)
      this.$emit('input', this.choosedData)
      let choosedNum = this.tableData.filter(item => {
        return item.isChoosed
      }).length
      if (choosedNum === this.tableData.length) {
        this.checkedAll = true
      } else {
        this.checkedAll = false
      }
    },
    checkAll () {
      this.checkedAll = !this.checkedAll
      this.tableData.forEach(item => {
        this.$set(item, 'isChoosed', this.checkedAll)
      })
    },
    handleClick (obj) {
      this.$emit('handleClick', obj)
    },
    wrapWidthFn () {
      let sum = 0;
      if (this.hasCheck) {
        sum += 46;
      }
      this.columns.forEach(obj => {
        if (obj.width) {
          sum += parseInt(obj.width)
        } else {
          sum += 90
        }
      })
      if (this.handle) {
        sum += this.handle.length * 90
      }
      if (sum > this.$refs.box.offsetWidth) {
        return `${sum}px`;
      } else {
        return 'auto';
      }
    }
  },
  mounted () {
    this.$nextTick(() => {
      this.wrapWidth = this.wrapWidthFn();
    })
  }
}
</script>