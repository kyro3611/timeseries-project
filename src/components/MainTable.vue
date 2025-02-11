<template>
    <div class="table-wrapper">
        <div class="table-container">
            <table>
                <div v-if="loading" class="spinner-container">
                    <div class="spinner"></div>
                </div>
                <thead>
                    <tr>
                        <th>Date and Time</th>
                        <th v-if="visibility.DE">DE</th>
                        <th v-if="visibility.GR">GR</th>
                        <th v-if="visibility.FR">FR</th>
                    </tr>
                </thead>
                <tbody>
                    <div v-if="!loading && !data.length" class="no-data-container">
                        No Data for this date range
                    </div>
                    <tr v-for="(item, index) in data" :key="index">
                        <td class="time-column">{{ item.formattedDate }}</td>

                        <td v-if="visibility.DE" contenteditable="true"
                            @focus="storePreviousValue(index, 'ENTSOE_DE_DAM_Price')"
                            @blur="updateValue(index, 'ENTSOE_DE_DAM_Price', $event)">{{ item.ENTSOE_DE_DAM_Price }}
                        </td>

                        <td v-if="visibility.GR" contenteditable="true"
                            @focus="storePreviousValue(index, 'ENTSOE_GR_DAM_Price')"
                            @blur="updateValue(index, 'ENTSOE_GR_DAM_Price', $event)">{{ item.ENTSOE_GR_DAM_Price }}
                        </td>

                        <td v-if="visibility.FR" contenteditable="true"
                            @focus="storePreviousValue(index, 'ENTSOE_FR_DAM_Price')"
                            @blur="updateValue(index, 'ENTSOE_FR_DAM_Price', $event)">{{ item.ENTSOE_FR_DAM_Price }}
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>

export default {
    props: {
        data: {
            type: Array,
            required: true,
        },
        loading: {
            type: Boolean,
            required: true,
        },
        startDate: {
            type: String,
            required: true,
        },
        endDate: {
            type: Date,
            required: true,
        },
        visibility: {
            type: Object,
            required: true,
        }
    },
    data() {
        return {
            previousValues: {},
        }
    },
    methods: {
        //save previous value for failed validation
        storePreviousValue(index, key) {
            this.previousValues[`${index}-${key}`] = this.data[index][key];
        },
        updateValue(index, key, event) {

            const newValue = event.target.innerText;
            //validation check
            if (isNaN(newValue)) {
                alert("Invalid input. Value must be a number between -200 and 200.");
                event.target.innerText = this.previousValues[`${index}-${key}`];
                return;
            } else if (newValue < -200 || newValue > 200) {
                alert("Invalid input. Value must be between -200 and 200.");
                //revert previous value
                event.target.innerText = this.previousValues[`${index}-${key}`];
                return;
            }

            this.$emit("update-data", { index, key, newValue });
        },
    },
};
</script>

<style>
.spinner-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100px;
    margin-left: 200px;
}

.spinner {
    width: 40px;
    height: 40px;
    border: 4px solid #ccc;
    border-top: 4px solid #49708a;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% {
        transform: rotate(0deg);
    }

    100% {
        transform: rotate(360deg);
    }
}

.table-wrapper {
    justify-content: center;
    align-items: center;
    width: 80%;
    height: 100vh;
}

.table-container {
    margin-top: 0;
    height: calc(100vh - 50px);
    overflow-y: auto;
    overflow-x: auto;
    border: 1px solid #ccc;
}

table {
    margin-top: 0;
    width: 100%;
    border-collapse: collapse;
}

thead {
    position: sticky;
    top: 0;
    z-index: 900;
}

td {
    padding: 10px;
    border: 1px solid #ccc;
    text-align: center;
    white-space: nowrap;
}

th {
    position: sticky;
    border-right: 1px solid #ccc;
    top: 0;
    padding: 20px;
    background-color: #49708a;
    color: white;
}

.time-column {
    background-color: #49708a;
    color: white;
}

.no-data-container {
    margin-top: 30%;
    margin-left: 20%;
    font-size: 2vw;
}

.show-hide-container {
    margin-bottom: 10px;
}

.eye-icon-container {
    margin-left: 10px;
}

.eye-icon {
    cursor: pointer;
}
</style>
