<template>
  <div class="nav-menu">
    <div class="logo">
      <img class="img" src="~@/assets/img/logo.svg" alt="logo" />
      <span class="title">Vue3+TS</span>
    </div>
    <el-row class="tac">
      <el-col>
        <el-menu
          class="el-menu-vertical-demo"
          background-color="#0c2135"
          :collapse="collapse"
          text-color="#b7bdc3"
          active-text-color="#0a60bd"
          :default-active="defaultValue"
        >
          <template v-for="item in userMeus" :key="item.id">
            <!-- 二级菜单 -->
            <template v-if="item.type === 1">
              <!-- 二级菜单可以展开的标题 -->
              <el-sub-menu :index="item.id + ''" class="el-submenu">
                <template #title>
                  <i v-if="item.icon" :class="item.icon"></i>
                  <span>{{ item.name }}</span>
                </template>
                <!-- 遍历二级菜单的item -->
                <template v-for="subitem in item.children" :key="subitem.id">
                  <el-menu-item
                    :index="subitem.id + ''"
                    class="el-menu-item"
                    @click="handleMenuItemClick(subitem)"
                  >
                    <i v-if="subitem.icon" :class="subitem.icon"></i>
                    <span>{{ subitem.name }}</span>
                  </el-menu-item>
                </template>
              </el-sub-menu>
            </template>
            <!-- 一级菜单 -->
            <template v-else-if="item.type === 2">
              <el-menu-item :index="item.id + ''">
                <i v-if="item.icon" :class="item.icno"></i>
                <span>{{ item.name }}</span>
              </el-menu-item>
            </template>
          </template>
        </el-menu>
      </el-col>
    </el-row>
  </div>
</template>

<script lang="ts">
import { defineComponent, computed, ref } from 'vue'
import { useStore } from '@/store'
import { useRouter, useRoute } from 'vue-router'

import { pathMapToMenu } from '@/utils/map-menus'

export default defineComponent({
  props: {
    collapse: {
      type: Boolean,
      default: false
    }
  },
  setup() {
    const store = useStore()
    const userMeus = computed(() => store.state.login.userMenus)

    const router = useRouter()
    const route = useRoute()
    const currentPath = route.path

    // data
    const menu = pathMapToMenu(userMeus.value, currentPath)
    const defaultValue = ref(menu.id + '')

    // event handler
    const handleMenuItemClick = (item: any) => {
      router.push({
        path: item.url ?? 'not-found'
      })
    }

    return {
      userMeus,
      handleMenuItemClick,
      defaultValue
    }
  }
})
</script>

<style scoped lang="less">
.nav-menu {
  height: 100%;
  background-color: #001529;

  .logo {
    display: flex;
    height: 28px;
    padding: 12px 10px 8px 10px;
    flex-direction: row;
    justify-content: flex-start;
    align-items: center;

    .img {
      height: 100%;
      margin: 0 10px;
    }

    .title {
      font-size: 16px;
      font-weight: 700;
      color: white;
    }
  }

  .el-menu {
    border-right: none;
  }

  // 目录
  .el-submenu {
    background-color: #001529 !important;
    // 二级菜单 ( 默认背景 )
    .el-menu-item {
      padding-left: 50px !important;
      background-color: #0c2135 !important;
    }
  }

  ::v-deep .el-submenu__title {
    background-color: #001529 !important;
  }

  // hover 高亮
  .el-menu-item:hover {
    color: #fff !important; // 菜单
  }

  .el-menu-item.is-active {
    color: #fff !important;
    background-color: #0a60bd !important;
  }
}

.el-menu-vertical:not(.el-menu--collapse) {
  width: 100%;
  height: calc(100% - 48px);
}
</style>
