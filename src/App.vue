<script setup>
// import p5image from './components/p5image.vue'
import ImgRender from './components/imageRender.vue'
import Convolution from './components/ConvolutionKernelEditor.vue'
</script>

<template>
  <div class="wrapper">
      <div class="convwrapper">
        <Convolution @kernel-change="changeKernel"/>
      </div>
      <div class="imgwrapper">
        <ImgRender :kernels="kernel" ref="image"/>
      </div>
  </div>


  <main>
    <!-- <p5image/> -->
  </main>
</template>

<script>

export default {
    name: "ConvolutionApp",
    data() {
      return {
        kernels: [
                [
                    [1., 1., 1.], 
                    [0., 1., 0.], 
                    [-1., -1., -1.]
                ]
            ],
      }
    },
    methods: {
      changeKernel: function (newKernels) {
        console.log("Changing kernel")
        this.kernels = newKernels
        this.$refs.image.updateConvolution(newKernels)
      }
    }
  }
</script>

<style scoped>
@media (min-width: 1024px) {
  .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
    width: 100vw;
    margin: 0;
  }

  #app {
    margin: 0 0 !important;
    padding: 0 !important;
    max-width: 100vw;
    width: 100vw;
    background: #EF8354 !important;
  }

  body {
    background: #EF8354 !important;
  }

  .imgwrapper {
    margin: auto;
    width: 40%;
    left: 0;
  }

  .convwrapper {
    width: 50vw;
    right: 0;
    margin: 0;
    min-height: 100vh;
    display:flex;
  }
}
</style>
