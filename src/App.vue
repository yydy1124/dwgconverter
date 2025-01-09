<template>
  <el-config-provider namespace="ep"> 
    <div class="main-container flex">   
      <div w="full" py="4">
         <BaseHeader />
         <Logos />
        <HelloWorld />
      </div>
    </div>
  </el-config-provider>
</template>

<style>
#app {
  text-align: center;
  color: var(--ep-text-color-primary);
}

.main-container {
  height: calc(100vh - var(--ep-menu-item-height) - 4px);
}
</style>
