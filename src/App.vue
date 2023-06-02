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
        <ImgRender :kernel="kernel" ref="image"/>
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
        kernel: [
                [
                    [1., 1., 1.], 
                    [0., 1., 0.], 
                    [-1., -1., -1.]
                ]
            ],
      }
    },
    methods: {
      changeKernel: function (newKernel) {
        console.log("Changing kernel")
        this.kernel = newKernel
        this.$refs.image.updateConvolution(newKernel)
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
  }

  .imgwrapper {
    margin: auto;
    width: 40%;
  }

  .convwrapper {
    width: 40%;
    
    margin: auto;
  }
}
</style>
