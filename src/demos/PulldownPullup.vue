<template>
  <div>
    <divider>下拉刷新和上拉加载更多组合</divider>
    <scroller lock-x scrollbar-y use-pullup use-pulldown height="200px" @pullup:loading="loadMore" @pulldown:loading="refresh" :pullStatus="pullStatus" ref="scroller">
      <div class="box2">
        <p v-for="i in n">placeholder {{i}}</p>
      </div>
      <!--pullup slot-->
      <div slot="pullup" class="xs-plugin-pullup-container xs-plugin-pullup-up" style="position: absolute; width: 100%; height: 40px; bottom: -40px; text-align: center;">
        <span v-show="pullStatus.up === 'default'"></span>
        <span class="pullup-arrow" v-show="pullStatus.up === 'down' || pullStatus.up === 'up'" :class="{'rotate': pullStatus.up === 'up'}">↑</span>
        <span v-show="pullStatus.up === 'loading'"><spinner type="ios-small"></spinner></span>
      </div>
    </scroller>
    <group>
      <x-switch :title="pullupEnabled ? '禁用Pullup' : '启用Pullup'" :value="true" @on-change="changePullupStatus"></x-switch>
    </group>

    <divider>上拉加载重置</divider>
    <scroller lock-x scrollbar-y use-pullup height="200px" @pullup:loading="loadMore1" ref="scroller1">
      <div class="box2">
        <p v-for="j in n1">placeholder {{j}}</p>
      </div>
    </scroller>
  </div>
</template>

<script>
import { Scroller, Divider, XSwitch, Group, Spinner } from '../components'

export default {
  components: {
    Scroller,
    Divider,
    XSwitch,
    Group,
    Spinner
  },
  methods: {
    loadMore (that) {
      setTimeout(() => {
        this.n += 10
        this.$nextTick(() => {
          // this.$broadcast('pullup:reset', uuid)
          that.$emit('pullup:reset', that.uuid)
        })
      }, 2000)
    },
    refresh (that) {
      setTimeout(() => {
        this.n = 10
        this.$nextTick(() => {
          // this.$broadcast('pulldown:reset', uuid)
          that.$emit('pulldown:reset', that.uuid)
        })
      }, 2000)
    },
    changePullupStatus (enabled) {
      debugger
      if (enabled) {
        // this.$broadcast('pullup:enable', this.$refs.scroller.uuid)
        this.$refs.scroller.$emit('pullup:enable',this.$refs.scroller.uuid)
        this.pullupEnabled = true
      } else {
        // this.$broadcast('pullup:disable', this.$refs.scroller.uuid)
        this.$refs.scroller.$emit('pullup:disable',this.$refs.scroller.uuid)
        this.pullupEnabled = false
      }
    },
    loadMore1 (that) {
      setTimeout(() => {
        this.n1 += 10
        this.$nextTick(() => {
          // this.$broadcast('pullup:reset', uuid)
          that.$emit('pullup:reset', that.uuid)
          if (this.n1 >= 30) {
            // this.$broadcast('pullup:disable', uuid)
            that.$emit('pullup:disable', that.uuid)
            console.log('No more data, Pullup disabled!')
          }
        })
      }, 2000)
    }
  },
  data () {
    return {
      n: 10,
      n1: 10,
      pullupEnabled: true,
      // pullupStatus: 'default'
      pullStatus:{
        down:'default',
        up:'default'
      }
    }
  }
}
</script>
<style lang="less" scoped>
.box2-wrap {
  height: 300px;
  overflow: hidden;
}
.rotate {
  display: inline-block;
  transform: rotate(-180deg);
}
.pullup-arrow {
  transition: all linear 0.2s;
  color: #666;
  font-size: 25px;
}
</style>
