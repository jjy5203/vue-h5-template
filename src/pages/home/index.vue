<template>
  <div>
    <van-address-list
      v-model="chosenAddressId"
      :list="list"
      default-tag-text="默认"
      @add="onAdd"
      @edit="onEdit"
    />
    <baidu-map
      :center="center"
      :zoom="zoom"
      style="width:100%;height:100%"
      :scroll-wheel-zoom="true"
      @ready="handler"
      @click="getClickInfo"
    />
    <div class="ceshi" @click="ceshi">
      <img src="@/assets/loading/loading.png" alt>
    </div>
    <van-button type="default" @click="changeTitle">修改标题</van-button>
    <van-cell title="单元格" value="内容" />
    <van-cell title="单元格" value="内容" label="描述信息" />
  </div>
</template>

<script>
// eslint-disable-next-line no-unused-vars
import { testInteractor, testHttpInteractor } from '@/core'
export default {
  name: 'Home',
  data() {
    return {
      chosenAddressId: undefined,
      list: [],
      query: {
        page: 1,
        count: 10
      },
      center: { lng: 109.45744048529967, lat: 36.49771311230842 },
      zoom: 13
    }
  },
  created() {
    this.$bus.on('test-change', item => {
      this.getTestList(this.query)
    })
    this.getTestList({ page: 1, count: 10 })
  },
  methods: {
    // async ceshi() {
    //   const options = {
    //     a: 'b',
    //     c: 'd'
    //   }
    //   await testHttpInteractor.getTest(options)
    // },
    changeTitle() {
      console.log('changeTitle')
      this.$bus.emit('changePageTitle', '000')
    },
    ceshi() {
      console.log('ceshi')
    },
    onAdd() {
      this.$router.push({ name: 'CreateTest' })
    },
    onEdit(item, index) {
      this.$router.push({ name: 'EditTest', params: { id: item.id }})
    },
    async getTestList(query) {
      try {
        this.query = Object.assign({}, this.query, query)
        const { data, total } = await testInteractor.getTestList(this.query)
        this.listTotal = total
        if (this.query.page === 1) {
          this.list = data
        } else {
          this.list = [...this.list, ...data]
        }
      } catch (error) {
        console.log(error)
      }
    },
    handler({ BMap, map }) {
      // 初始化地图,设置中心点坐标
      var point = new BMap.Point(119.8025089500, 25.4890556400)
      map.centerAndZoom(point, 13)

      // 添加鼠标滚动缩放
      map.enableScrollWheelZoom()
      // 添加缩略图控件
      map.addControl(new BMap.OverviewMapControl({ isOpen: false, anchor: BMAP_ANCHOR_BOTTOM_RIGHT }))
      // 添加缩放平移控件
      map.addControl(new BMap.NavigationControl())
      // 添加比例尺控件
      map.addControl(new BMap.ScaleControl())
      // 添加地图类型控件
      // map.addControl(new BMap.MapTypeControl());

      // 设置标注的图标
      var icon = new BMap.Icon('./static/img/map.png', new BMap.Size(100, 100))
      // 设置标注的经纬度
      var marker = new BMap.Marker(new BMap.Point(121.160724, 31.173277), { icon: icon })
      // 把标注添加到地图上
      map.addOverlay(marker)
      var content = '<table>'
      content = content + '<tr><td> 编号：001</td></tr>'
      content = content + '<tr><td> 地点：上海汉得信息技术股份有限公司</td></tr>'
      content = content + '<tr><td> 时间：2018-1-3</td></tr>'
      content += '</table>'
      var infowindow = new BMap.InfoWindow(content)
      // 图标点击的时候显示标注
      marker.addEventListener('click', function() {
        this.openInfoWindow(infowindow)
      })
      // 标注默认显示
      // var infoWindow = new BMap.InfoWindow(content) // 创建信息窗口对象
      // map.openInfoWindow(infoWindow, point)
    },
    getClickInfo(e) {
      console.log(e.point.lng)
      console.log(e.point.lat)
      this.center.lng = e.point.lng
      this.center.lat = e.point.lat
    }
  }
}
</script>

<style lang="scss" scoped>
.ceshi {
  font-size: 16px;
  img {
    width: 50px;
    // height: 50px;
  }
}
</style>

