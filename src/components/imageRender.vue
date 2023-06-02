<template>
    <div class="canvas-display">
        <canvas v-show="!bOrA" id="input"></canvas>
        <canvas v-show="bOrA" id="output"></canvas>
    </div>
</template>

<script>

import * as tf from '@tensorflow/tfjs'
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
        updateConvolution: function (kernel) {
            let output = this.convolutionMatrix(this.imageData, kernel, 0);
            this.outputCtx.putImageData(output, 0, 0);
        },
        convolutionMatrix: function (input, kernel, offset = 0) {
            console.log("Starting convolution!")
            let ctx = this.outputCtx;
            let output = ctx.createImageData(input);
            let w = input.width,
                h = input.height;
            let iD = input.data,
                oD = output.data;
            for (let y = 1; y < h - 1; y += 1) {
                for (let x = 1; x < w - 1; x += 1) {
                    for (let c = 0; c < 3; c += 1) {
                        let i = (y * w + x) * 4 + c;
                        oD[i] =
                        offset +
                        (kernel[0][0] * iD[i - w * 4 - 4] +
                            kernel[0][1] * iD[i - w * 4] +
                            kernel[0][2] * iD[i - w * 4 + 4] +
                            kernel[1][0] * iD[i - 4] +
                            kernel[1][1] * iD[i] +
                            kernel[1][2] * iD[i + 4] +
                            kernel[2][0] * iD[i + w * 4 - 4] +
                            kernel[2][1] * iD[i + w * 4] +
                            kernel[2][2] * iD[i + w * 4 + 4]) /
                            this.divisor;
                    }
                    oD[(y * w + x) * 4 + 3] = 255;
                }
            }
            return output
        },
        convolve: function(kernels) {
            let image = tf.browser.fromPixels(this.ogImageData, 3);
            let ogimage = image;
            image = image.cast("float32")
            for (let i = 0; i < kernels.length; i++) {
                const [red, green, blue] = tf.split(image, 3, 2);

                let kernel = kernels[i];
                // const filter = tf.tensor2d(kernel);
                let filter = tf.tensor2d(
                    kernel
                );
                // filter = filter.transpose([2, 3, 0, 1]);

                filter = filter.expandDims(2).expandDims(3);
                // Remove the extra dimensions
                filter.print(true)
                red.max().print()
                const redResult = tf.conv2d(red, filter, [1, 1], 'valid');
                const greenResult = tf.conv2d(green, filter, [1, 1], 'valid');
                const blueResult = tf.conv2d(blue, filter, [1, 1], 'valid');

                // const redNormalizedResult = tf.div(tf.sub(redResult, redResult.min()), tf.sub(redResult.max(), redResult.min())).mul(255);
                // const blueNormalizedResult = tf.div(tf.sub(blueResult, blueResult.min()), tf.sub(blueResult.max(), blueResult.min())).mul(255);
                // const greenNormalizedResult = tf.div(tf.sub(greenResult, greenResult.min()), tf.sub(greenResult.max(), greenResult.min())).mul(255);
                

                const combinedResult = tf.stack([redResult, greenResult, blueResult], 2);

                // const normalizedResult = tf.div(tf.sub(combinedResult, combinedResult.min()), tf.sub(combinedResult.max(), combinedResult.min())).mul(255);
                // const normalizedResult = combinedResult.div(combinedResult.max()).mul(255);
                // normalizedResult.max().print()
                // normalizedResult.min().print()
                const clampedResult = tf.clipByValue(combinedResult, 0, 255).div(tf.scalar(255.0));
                
                image = clampedResult.squeeze();
                console.log('imagemaxes')
                image.max().print()
                image.min().print()
            }

            image.print(true)        
            return image;
        },
        draw: function (img, width, height, newKern = false) {
            tf.setBackend('cpu')
            // const imageGet = require('get-image-data')
            this.loading = true;

            // if (newKern) {
            //     this.custom = this.kernel;
            // }

            let display = document.querySelector(".canvas-display");
            let input = document.querySelector("#input");
            let output = document.querySelector("#output");

            display.removeChild(input);
            display.removeChild(output);

            this.inputCtx = input.getContext("2d");

            this.outputCtx = output.getContext("2d");

            if (80 / 48 < width / height) {
                output.width = input.width = 800;
                output.height = input.height = (800 * height) / width;
                let top = 240 - (400 * height) / width;
                output.style.top = input.style.top = top + "px";
                output.style.left = input.style.left = "0px";
                display.appendChild(input);
                display.appendChild(output);
            } else {
                output.height = input.height = 480;
                output.width = input.width = (480 * width) / height;
                let left = 400 - (240 * width) / height;
                output.style.top = input.style.top = "0px";
                output.style.left = input.style.left = left + "px";
                display.appendChild(input);
                display.appendChild(output);
            }

            let ctx = this.inputCtx;
            
            ctx.drawImage(img, 0, 0, input.width, input.height);
            
            let imageData = ctx.getImageData(0, 0, input.width, input.height);
            this.ogImageData = imageData;
            // output = this.convolutionMatrix(imageData, this.custom, 0);
            // // output = this.convolutionMatrix(output, this.custom, 0);
            let convolved_img = this.convolve(this.kernels);
            // convolved_img.print(true);
            tf.browser.draw(convolved_img, output);
            
            // this.outputCtx.putImageData(image, 0, 0);
        },
    },
    mounted() {
        let vm = this;
        let img = new Image();
        img.src = "./public/mystical.jpg";
        img.onload = function() {
            vm.draw(this, this.width, this.height);
        };
    }
};
</script>