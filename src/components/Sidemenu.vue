<template>
  <div class="sidemenu">
    <div class="logo-sidemenu">
      <div class="sidemenu-title">
        Falcon Note Taking
      </div>
    </div>
    <el-menu
    :default-active="activeIndex"
    background-color="#158CBA"
    text-color="#ffffff"
    class="sidemenu-inner"
    >
    <div class="flex-grow" />
    <div class="menu-item-wrapper" v-for="menuItem in menuItems">
      <el-menu-item :index="menuItem.index" v-if="menuItem.type === 'menu-item'">
        <a :href="menuItem.link">
          <i :class="menuItem.icon"></i>
          {{ menuItem.title }}
        </a>
      </el-menu-item>
      <el-sub-menu :index="menuItem.index" v-else>
        <template #title>
          <i :class="menuItem.icon"></i>
          <a href="#">{{ menuItem.title }}</a>
        </template>
        <el-menu-item v-for="subItem in menuItem.submenu" :index="subItem.index">
          <a :href="subItem.link">{{ subItem.title }}</a>
        </el-menu-item>
      </el-sub-menu>
    </div>
  </el-menu>
  </div>
</template>

<script>

export default {
  name: 'Sidemenu',
  data(){
    return{
      activeIndex: '1',
      menuItems: [
        {
          'index': '1',
          'type': 'menu-item',
          'icon': 'bi bi-house-door-fill',
          'title': 'Dashboard',
          'link': '/dashboard',
        },
        {
          'index': '2',
          'type': 'submenu',
          'icon': 'bi bi-filter-square-fill',
          'title': 'Notes',
          'submenu': [
            {
              'index': '2-1',
              'title': 'Notes',
              'link': '/notes',
            },
            {
              'index': '2-1',
              'title': 'Notebooks',
              'link': '/notebooks',
            },
          ],
        },
        {
          'index': '3',
          'type': 'menu-item',
          'icon': 'bi bi-gear-fill',
          'title': 'Settings',
          'link': '/settings',
        },
      ],
    }
  },
  mounted(){
    this.checkActive();
  },
  methods:{
    //Check current active menu item
    checkActive(){
      let activeIndex = '';
      const currentPath = window.location.pathname;
      for(let i = 0; i < this.menuItems.length; i++){
        const menuItem = this.menuItems[i];
        //If the menu item do not had submenu
        if(menuItem.type === 'menu-item'){
          if(menuItem.link === currentPath){
            activeIndex = menuItem.index;
            return;
          }
        }else{
          //If the menu item had submenu
          menuItem['submenu'].forEach(function(item){
            if(item.link === currentPath){
              activeIndex = item.index;
              return;
            }
          });
        }
      }
      this.activeIndex = activeIndex;
    },
  },
}
</script>

<style scoped lang="scss">
.logo-sidemenu{
  padding-top: 15px;
  padding-bottom: 15px;
  margin-left: 25px;
  color: white;
  font-size: 1.3em;
  font-weight: bold;
  display: flex;
  align-items: center;
  img{
    width: 30%;
  }
}
.sidemenu{
  width: 270px;
  background: #158CBA;
  overflow: hidden;
  overflow-y: hidden;
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  min-height: 100vh;

  a{
    color: white;
    font-size: 16px;
    &:hover{
      text-decoration: none;
    }
  }

  .bi{
    margin-right: 10px;
    font-size: 1.3em;
    &.bi-filter-square-fill{
      margin-right: 16px;
    }
  }

  .sidemenu-menu{
    border-right: 0;
  }
}
</style>
