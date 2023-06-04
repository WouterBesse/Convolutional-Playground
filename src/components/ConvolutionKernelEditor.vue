<template>
    <div class="convwrapper">
        <TransitionGroup appear name="list" >
            <div class="convtablewrap" v-for="k in kernel_amount" :key="k">
                <div class="kerneltitle">
                    Convolution: {{ k }}   
                </div>   
                <table class="convtable" >
                    
                    <tr v-for="r in rows">
                        <td v-for="c in cols" class="cell">
                            <input class="conv-input"
                                type="number"
                                max="64"
                                min="-64"
                                :value="convData[k - 1][r-1][c-1]"
                                @focus="$event.target.value = ''"
                                @input="set_convData($event.target.value, k-1, r-1, c-1)"
                                @blur="$event.target.value = convData[k - 1][r-1][c-1]; send_convData()">
                                <!-- <input class="conv-input"
                                type="number"
                                v-model="convData[k - 1][r-1][c-1]"
                                > -->   
                        </td>
                    </tr>
                </table>
            </div>
        </TransitionGroup>
    </div>
    <div class="buttons">
        <div class="plusicon" @click="add_kernel">
            +
        </div>
        <div class="plusicon" @click="remove_kernel">
            -
        </div>
    </div>
</template>

<script>
// require('@tensorflow/tfjs-backend-cpu')
// require('@tensorflow/tfjs-backend-webgl')

export default {
    name: "Convolution",
    data() {
        return {
            rows: 3,
            cols: 3,
            kernel_amount: 1,
            convData: [
                [
                    [1., 1., 1.], 
                    [0., 1., 0.], 
                    [-1., -1., -1.],
                ],
            ]
        }
    },
    emits: ["kernel-change"],
    watch: {
        convData(matrix) {
            matrix.map(val => Number(val) || 0);
            console.log("blaas")
            // this.$emit("kernel-change", this.convData)
        }
    },
    methods: {
        populate_table: function () {
            for (let k = 0; k < this.kernel_amount; k++) {
                for (let i = 0; i < this.rows; i++) {
                    // let row = []
                    for (let j = 0; j < this.cols; j++) {
                        this.convData[k][i][j] = 0.
                    }
                    // .push(row)
                }
            }
        },
        set_convData: function (value, kernel, row, col) {
            value = parseFloat(value)
            this.convData[kernel][row][col] = value
        },
        send_convData: function () {
            this.$emit("kernel-change", this.convData)
        },
        add_kernel: function () {
            
            this.convData.push([
                [0., 0., 0.], 
                [0., 1., 0.], 
                [0., 0., 0.],
            ])
            this.kernel_amount += 1
        },
        remove_kernel: function () {
            if (this.kernel_amount > 1) {
                this.convData.pop()
                this.kernel_amount -= 1
            }
        }
    },
    beforeMount() {
        this.populate_table()
        console.log("convdata", this.convData)
        // this.$emit("kernelChange", this.convData)
    }
}
</script>

<style scoped>
.convwrapper {
    color:black;
    margin: auto;
    display: flex;
    width: 50vw;
    flex-wrap:wrap;
    justify-content: center;
}

.convtable {
    /*border: black 3px solid;*/
    border-collapse: separate;
    border-spacing: 0px;
    /* margin: auto; */
    /* margin: 10px; */
}

.convtablewrap {
    margin: 10px;
}

.kerneltitle {
    margin: auto;
    text-align: center;
    font-size: 1.2em;
    font-weight: 600;
}

.cell {
    padding: 0px;
    margin: 0px;
    width: 4em !important;
    max-width: 4em;
    height: 4em;
    text-align: center;
    vertical-align: center;
}

.convtable td {
    border: black 2px solid;
    vertical-align: top;
}

/* Ugly stuff to make a rounded table */
tr:first-of-type td {
    border-top: black 4px solid;
}

tr:last-of-type td {
    border-bottom: black 4px solid;
}
tr:first-of-type td:first-of-type {
    border-top-left-radius: 10px;
    border-left: black 4px solid;
}
tr:first-of-type td:last-of-type {
    border-top-right-radius: 10px;
    border-right: black 4px solid;
}
tr:last-of-type td:first-of-type {
    border-bottom-left-radius: 10px;
    border-left: black 4px solid;
}
tr:last-of-type td:last-of-type {
    border-bottom-right-radius: 10px;
    border-right: black 4px solid;
}
.convtable tr:not(:first-child):not(:last-child) td:first-of-type {
    border-left: black 4px solid;
}
.convtable tr:not(:first-child):not(:last-child) td:last-of-type {
    border-right: black 4px solid;
}

.conv-input {
    border: 0 !important;
    border-color:transparent;
    background-color: transparent;
    text-align: center;
    width: 4em;
    height: 4em;
}

.conv-input:focus {
    border: 0 !important;
    border-color:transparent !important;
    outline:none;
}

.conv-input::-webkit-inner-spin-button, .conv-input::-webkit-outer-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

.buttons {
    display: flex;
    justify-content: center;
    align-items: center;
    margin: auto;
    width: 50vw;
}
.plusicon {
    width: 1em;
    height: 1em;
    border: 0 !important;
    padding: 0px;
    border-color:transparent !important;
    background-color: red;
    text-align: center;
    font-size: 3em;
    line-height: 0.8em;
    border-radius: 100%;
    color: black;
    cursor: pointer;
    vertical-align: center;
    /* margin: auto; */
    margin: 10px;
}

.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
</style>