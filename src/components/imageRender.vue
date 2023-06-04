<template>
    <div class="canvas-display">
        <canvas v-show="!bOrA" id="input"></canvas>
        <canvas v-show="bOrA" id="output"></canvas>
    </div>
</template>

<script>

import * as tf from '@tensorflow/tfjs'
import '@tensorflow/tfjs-backend-webgpu'

// import * as tf from '@tensorflow/tfjs-backend-webgl'
export default {
    name: "ImageCanvas",
    props: {
        kernel: Array,
    },
    data() {
        return {
            loading: false,
            inputCtx: "",
            outputCtx: "",
            width: 800,
            height: 480,
            bOrA: true,
            divisor: 1,
            kernels: [
                [
                    [1., 1., 1.], 
                    [0., 1., 0.], 
                    [-1., -1., -1.]
                ]
            ],
            imageData: "",
            ogImageData: ""
        }
    },
    methods: {
        updateConvolution: function (kernels) {
            let output = document.querySelector("#output");
            let convolved_img = this.convolve(kernels);
            // convolved_img.print(true);
            // tf.browser.draw(convolved_img, output);
            tf.browser.toPixels(convolved_img, output);
        },
        convolve: function(kernels) {
            console.log(tf.version)
            let image = tf.browser.fromPixels(this.ogImageData, 3)
            image = image.cast("float32")
            image = image.div(255)
            image.dataToGPU();
            let ogimage = image;
            console.log("kernels", kernels)
            
            for (let i = 0; i < kernels.length; i++) {
                const [red, green, blue] = tf.split(image, 3, 2);
                
                let kernel = kernels[i];
                console.log(kernel)
                let filter = tf.tensor2d(kernel);

                filter = filter.expandDims(2).expandDims(3);

                const redResult = tf.conv2d(red, filter, [1, 1], 'same');
                const greenResult = tf.conv2d(green, filter, [1, 1], 'same');
                const blueResult = tf.conv2d(blue, filter, [1, 1], 'same');                

                const combinedResult = tf.stack([redResult, greenResult, blueResult], 2);

                // const normalizedResult = tf.div(tf.sub(combinedResult, combinedResult.min()), tf.sub(combinedResult.max(), combinedResult.min())).mul(255);
                // const normalizedResult = combinedResul   t.div(combinedResult.max()).mul(255);
                
                // const normalizedImage = combinedResult.sub(combinedResult.min()).div(combinedResult.max().sub(combinedResult.min()));
                
                image = combinedResult.squeeze();
                // console.log('imagemaxes')
                // image.max().print()
                // image.min().print()
            }   
            // ogimage.mean(2).print(true)
            // image.print(true)
            // image = tf.batchNorm(image, ogimage.mean(2), tf.ones([480, 480]));
            const normalisedImage = image.sub(image.min()).div(image.max().sub(image.min()));
            // image.max().print(true)
            // image.min().print(true)
            const clampedResult = tf.clipByValue(image.mul(255), 0, 255);
            let newimage = clampedResult.cast("int32");

            // image.print(true)        
            return newimage;
        },
        draw: function (img, width, height, newKern = false) {
            //tf.setBackend('cpu')
            // const imageGet = require('get-image-data')
            this.loading = true;

            // if (newKern) {
            //     this.custom = this.kernel;
            // }

            let display = document.querySelector(".canvas-display");
            let output = document.querySelector("#output");

            display.removeChild(output);

            this.outputCtx = output.getContext("2d");

            if (80 / 48 < width / height) {
                output.width = 800;
                output.height = (800 * height) / width;
                let top = 240 - (400 * height) / width;
                output.style.top = top + "px";
                output.style.left = "0px";
                display.appendChild(output);
            } else {
                output.height = 480;
                output.width = (480 * width) / height;
                let left = 400 - (240 * width) / height;
                output.style.top = "0px";
                output.style.left = left + "px";
                display.appendChild(output);
            }

            // let ctx = this.inputCtx;
            
            this.outputCtx.drawImage(img, 0, 0, output.width, output.height);
            
            let imageData = this.outputCtx.getImageData(0, 0, output.width, output.height);
            this.ogImageData = imageData;
            let convolved_img = this.convolve(this.kernels);

            // tf.browser.draw(convolved_img, output);
            tf.browser.toPixels(convolved_img, output);
        },
    },
    async mounted() {
        tf.setBackend('webgl');
        
        await tf.ready().then(() => {
            let vm = this;
            let img = new Image();
            img.src = "./public/mystical.jpg";
            img.onload = function() {
                vm.draw(this, this.width, this.height);
            }
        })
    },
        
};
</script>