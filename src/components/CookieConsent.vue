<template>
    <transition appear name="fade">
      <div class="cookie__overlay">
        <div class="modal__cookie__consent">
          Test modal
        </div>
      </div>
    </transition>
</template>
<script>
    export default {

    }
</script>
<style lang="scss">
  body {
    margin: 0 auto;
  }

  .cookie__overlay {
    display: flex;
    justify-content: center;
    align-items: center;
    position: fixed;
    height: 100%;
    width: 100%;
    background: rgba(0,0,0,0.5);
  }

  .modal__cookie__consent {
    background: white;
    width: 50%;
  }
</style>
