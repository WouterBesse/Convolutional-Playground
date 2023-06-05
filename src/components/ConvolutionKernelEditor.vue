<template>
    <div class="gridwrapper">
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
                                placeholdder=0
                                aria-label="conv-value"
                                aria-labelledby="firstname"
                                
                                onfocus="this.value=''"
                                max="64"
                                min="-64"
                                
                                @input="set_convData($event.target.value, k-1, r-1, c-1)"
                                @blur="$event.target.value = convData[k - 1][r-1][c-1]; send_convData()">
                                <!-- <input class="conv-input"
                                type="number"
                                v-model="convData[k - 1][r-1][c-1]"
                                :value="convData[k - 1][r-1][c-1]"
                                > -->   
                        </td>
                    </tr>
                </table>
            </div>
        </TransitionGroup>
        <div class="buttons">
        <div class="plusicon" @click="add_kernel">
            +
        </div>
        <div class="plusicon" @click="remove_kernel">
            -
        </div>
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
                    [0., 0., 0.], 
                    [0., 1., 0.], 
                    [0., 0., 0.],
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
            let grid = document.getElementsByClassName("convtable")
            console.log(grid, this.kernel_amount -1)
            grid = grid[this.kernel_amount -1]
            console.log(grid)
            for (let i = 0; i < this.rows; i++) {
                let row = grid.getElementsByTagName("tr")[i]
                // let row = []
                for (let j = 0; j < this.cols; j++) {
                    let cell = row.getElementsByTagName("td")[j].getElementsByTagName("input")[0]
                    cell.setAttribute('value',this.convData[this.kernel_amount - 1][i][j]);
                    // this.convData[k][i][j] = 0.;
                }
                // .push(row)
            }
            
        },
        set_convData: function (value, kernel, row, col) {
            console.log(value)
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
            this.populate_table()
            this.send_convData()
        },
        remove_kernel: function () {
            if (this.kernel_amount > 1) {
                this.convData.pop()
                this.kernel_amount -= 1
            }
        }
    },
    mounted() {
        this.populate_table()
        // console.log("convdata", this.convData)
        // this.$emit("kernelChange", this.convData)
    }
}
</script>

<style scoped>
.gridwrapper {
    color:#744253;
    margin: auto;
    display: flex;
    width: 50vw;
    flex-wrap:wrap;
    justify-content: center;
    padding: 25px;
}

.convtable {
    /*border: black 3px solid;*/
    border-collapse: separate;
    border-spacing: 0px;
    background-color: #F3D9DC;
    border-radius: 10px;
    color: #744253;
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
    /* max-width: 4em; */
    
    height: 4em;
    max-height: 4em !important;
    text-align: center;
    /* vertical-align: center; */
    color: #744253 !important;
    
}

.convtable td {
    border: #744253 2px solid;
    vertical-align: top;
}

/* Ugly stuff to make a rounded table */
tr:first-of-type td {
    border-top: #744253 4px solid;
}

tr:last-of-type td {
    border-bottom: #744253 4px solid;
}
tr:first-of-type td:first-of-type {
    border-top-left-radius: 10px;
    border-left: #744253 4px solid;
}
tr:first-of-type td:last-of-type {
    border-top-right-radius: 10px;
    border-right: #744253 4px solid;
}
tr:last-of-type td:first-of-type {
    border-bottom-left-radius: 10px;
    border-left: #744253 4px solid;
}
tr:last-of-type td:last-of-type {
    border-bottom-right-radius: 10px;
    border-right: #744253 4px solid;
}
.convtable tr:not(:first-child):not(:last-child) td:first-of-type {
    border-left: #744253 4px solid;
}
.convtable tr:not(:first-child):not(:last-child) td:last-of-type {
    border-right: #744253 4px solid;
}

.conv-input {
    border: 0 !important;
    border-color:transparent;
    background-color: transparent;
    text-align: center;
    margin: auto;
    line-height: 4.0em;
    font-weight: 600;
    font-size: 1.2em;
    padding: 0px;
    color:#744253;
}

.conv-input::placeholder {
    color: #744253;
    opacity: 100;
    font-weight: 600;
    font-size: 1.2em;
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
    background-color: #744253;
    text-align: center;
    font-size: 3em;
    line-height: 1.0em;
    font-weight: 500;
    border-radius: 100%;
    color: #F3D9DC;
    cursor: pointer;
    vertical-align: center;
    /* margin: auto; */
    margin: 10px;
    user-select: none;
    transition: all 0.2s ease;
}

.plusicon:hover {
    background-color: #C78283;
    box-shadow: 0px 0px 10px 0px #c78283a8;
    cursor: pointer;
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