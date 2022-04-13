<template>
  <div class="xiaop-menu">
    <ul class="menu1" @mouseleave="menuMouseleave()" ref="menu1Ref">
      <li v-for="item1, index1 in menu" @mouseover="menu1mouseover(item1, $event)" :key="index1" @click="goto(item1.path)">{{item1[title]}}</li>
      <ul class="menu2" v-show="showMenu2" :style="{top: menu2Top + 'px'}" ref="menu2Ref">
        <li v-for="item2, index2 in menu2" @mouseover="menu2mouseover(item2, $event)" :key="index2" @click="goto(item2.path)">{{item2[title]}}</li>
        <ul class="menu3" v-show="showMenu3" :style="{top: menu3Top + 'px', left: menu3Left + 'px'}" ref="menu3Ref">
          <li v-for="item3, index3 in menu3" :key="index3" >
            <div @click="goto(item3.path)">{{item3[title]}}</div>
            <template v-if="item3[childrenPropertyName] && item3[childrenPropertyName].length">
              <span v-for="item4, index4 in item3[childrenPropertyName]" :key="index4" @click="goto(item4.path)">{{item4[title]}}</span>
            </template>
          </li>
        </ul>
      </ul>
    </ul>
  </div>
</template>
<script>
export default {
  name: 'XiaopMenu',
  props: {
    // 菜单
    menu: {
      type: Array,
      default: () => []
    },
    // 子菜单列表字段名称
    childrenPropertyName: {
      type: String,
      default: 'children'
    },
    // 菜单名称显示字段
    title: {
      type: String,
      default: 'title'
    }
  },
  data () {
    return {
      // 二级菜单
      menu2: [],
      // 二级菜单显示隐藏
      showMenu2: false,
      // 二级菜单定位top值
      menu2Top: null,
      // 三级菜单
      menu3: [],
      // 三级菜单显示隐藏
      showMenu3: false,
      // 三级菜单定位top值
      menu3Top: null,
      // 三级菜单定位left值
      menu3Left: null
    }
  },
  methods: {
    // 点击菜单跳转
    goto (path) {
      if (!path) return
      this.$router.push(path)
    },
    // 菜单移出事件
    menuMouseleave () {
      this.showMenu2 = false
      this.showMenu3 = false
    },
    // 一级菜单移入事件
    menu1mouseover (menuItem, e) {
      if (menuItem[this.childrenPropertyName].length > 0) {
        this.showMenu2 = true
        this.menu2 = menuItem[this.childrenPropertyName]
      } else {
        this.showMenu2 = false
      }
      this.showMenu3 = false
      this.menu2Top = e.target.offsetTop - e.target.parentNode.scrollTop
      // 二级菜单顶部默认跟对应一级菜单平齐，当二级菜单高度超出屏幕时，做适当偏移
      this.$nextTick(() => {
        const menu1Height = this.$refs.menu1Ref.offsetHeight
        const menu2Height = this.$refs.menu2Ref.offsetHeight
        if (this.menu2Top + menu2Height > menu1Height) {
          this.menu2Top = menu1Height - menu2Height
        }
      })
    },
    // 二级菜单移入事件
    menu2mouseover (menuItem, e) {
      if (menuItem[this.childrenPropertyName].length > 0) {
        this.showMenu3 = true
        this.menu3 = menuItem[this.childrenPropertyName]
      } else {
        this.showMenu3 = false
      }
      this.menu3Top = e.target.offsetTop - 10
      this.menu3Left = e.target.parentNode.offsetWidth
      // 三级菜单顶部默认跟对应二级菜单平齐，当三级菜单高度超出屏幕时，做适当偏移
      this.$nextTick(() => {
        const menu1Height = this.$refs.menu1Ref.offsetHeight
        const menu3Height = this.$refs.menu3Ref.offsetHeight
        // 三级菜单超出高度等于 = 屏幕高度 - （二级菜单top值 + 三级菜单高度）
        const overflowHeight = menu1Height - (this.menu2Top + menu3Height)
        if (overflowHeight < 0) this.menu3Top = overflowHeight
      })
    }
  }
}
</script>
<style lang="scss" scope>
*{
  margin: 0;
  padding: 0;
}
ul{
  list-style: none;
}
.xiaop-menu {
  position: relative;
  height: 100%;
}
.menu1{
  width: 120px;
  height: 100%;
  padding: 10px 0;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #09B3B7;
  cursor: pointer;
  overflow: auto;
}
.menu1>li {
  width: 100px;
  line-height: 100px;
  text-align: center;
  color: #FFF;
  font-weight: bold;
}
.menu1>li:hover {
  border-radius: 5px;
  background-color: rgba(255, 255, 255, 0.2);
}
.menu2{
  padding: 10px;
  position: absolute;
  left: 120px;
  background-color: #F7F7F7;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
}
.menu2>li{
  padding: 5px 10px;
  &:hover {
    border-radius: 5px;
    background-color: #09B3B7;
    color: #fff;
  }
}
.menu3{
  padding: 20px;
  position: absolute;
  background-color: #fff;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
}
.menu3>li{
  width: 180px;
  color: #09B3B7;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}
.menu3>li>span{
  color: #000;
  font-size: 14px;
  margin: 5px 0;
}
/*滚动条整体粗细样式*/
::-webkit-scrollbar {
    /*高宽分别对应横竖滚动条的尺寸*/
    width: 8px;
    height: 8px;
}

/*滚动条里面小方块*/
::-webkit-scrollbar-thumb {
    border-radius: 10px !important;
    box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2) !important;
    /* 颜色 */
    background:#09B3B7 !important;
    /* 线性渐变背景 */
    // background-image: linear-gradient(45deg, #ffbd61 25%,#ffbd61 25%, #ff8800 25%, #ff8800 50%,#ffbd61 50%, #ffbd61 75%, #ff8800 75%, #ff8800 100%)!important;
}

/*滚动条轨道*/
::-webkit-scrollbar-track {
    border-radius: 10px !important;
    box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2) !important;
    background: #EDEDED !important;
}
</style>
