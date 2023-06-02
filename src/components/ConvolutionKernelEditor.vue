<template>
    <div class="convwrapper">
        <table class="convtable">
            <tr v-for="r in rows">
                <td v-for="c in cols">
                    <!-- <input class="conv-input"
                           type="number"
                           max="64"
                           min="-64"
                           :value="convData[r-1][c-1]"
                           @focus="$event.target.value = ''"
                           @input="set_convData($event.target.value, r-1, c-1)"
                           @blur="$event.target.value = convData[r-1][c-1]; send_convData()">-->
                        <input class="conv-input"
                           type="number"
                           max="64"
                           min="-64"
                           v-model="convData"
                           
                           >
                         
                           
                </td>
            </tr>
        </table>
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
                    [-1., -1., -1.]
                ]
            ],
        }
    },
    emits: ["kernel-change"],
    watch: {
        convData(matrix) {
            matrix.map(val => Number(val) || 0);
            console.log("blaas")
            this.$emit("kernel-change", this.convData)
        }
    },
    methods: {
        populate_table: function () {
            for (let k = 0; k < this.kernel_amount; k++) {
                for (let i = 0; i < this.rows; i++) {
                    // let row = []
                    for (let j = 0; j < this.cols; j++) {
                        this.convData[k][i][j] = 0
                    }
                    // .push(row)
                }
            }
        },
        set_convData: function (value, row, col) {
            value = parseFloat(value)
            this.convData[row][col] = value
        },
        send_convData: function () {
            this.$emit("kernel-change", this.convData)
        }
    },
    beforeMount() {
        this.populate_table()
        // this.$emit("kernelChange", this.convData)
    }
}
</script>

<style scoped>
.convwrapper {
    color:black;
}

.convtable {
    /*border: black 3px solid;*/
    border-collapse: separate;
    border-spacing: 0px;
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
</style>