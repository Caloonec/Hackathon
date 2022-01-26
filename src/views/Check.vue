<template>
  <div>
  <div class="wrapper">
    <transition name="fade">
    <svg v-if="show" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
         viewBox="0 0 98.5 98.5" xml:space="preserve">
  <path class="checkmark" fill="none" stroke-width="8" stroke-miterlimit="10" d="M81.7,17.8C73.5,9.3,62,4,49.2,4
	C24.3,4,4,24.3,4,49.2s20.3,45.2,45.2,45.2s45.2-20.3,45.2-45.2c0-8.6-2.4-16.6-6.5-23.4l0,0L45.6,68.2L24.7,47.3"/>

    </svg>

    </transition>


  </div>
  <transition name="fade">

    <p v-if="!show" >Bienvenue ..... sur ton espace personnel</p>
  </transition>
  </div>
</template>

<script>
export default {
  name: "CheckComponent",
  data() {
    return {
      show: true,
      timer: ''
    }
  },
  created () {
    this.timer = setInterval(this.fetchEventsList, 2000);
  },
  methods: {
    fetchEventsList () {
      this.show = false;
    },
    cancelAutoUpdate () {
      clearInterval(this.timer);
    }
  },
  beforeUnmount () {
    this.cancelAutoUpdate();
  }
}
</script>

<style scoped>
.wrapper {
  width: 100px;
  margin: 4em auto 0;
}

.checkmark {
  stroke: deepskyblue;
  stroke-dashoffset: 745.74853515625;
  stroke-dasharray: 745.74853515625;
  animation: dash 3s ease-out forwards;
}

@keyframes dash {
  0% {
    stroke-dashoffset: 745.74853515625;
  }
  100% {
    stroke-dashoffset: 0;
  }
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .6s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>