<template>
    <div class="table-wrapper">
        <div class="table-container">
            <table>
                <thead>
                    <tr>
                        <th>Date and Time</th>
                        <th>DE</th>
                        <th>GR</th>
                        <th>FR</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item, index) in data" :key="index">
                        <td class="time-column">{{ item.formattedDate }}</td>
                        <td>{{ item.ENTSOE_DE_DAM_Price }}</td>
                        <td>{{ item.ENTSOE_GR_DAM_Price }}</td>
                        <td>{{ item.ENTSOE_FR_DAM_Price }}</td>
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
        }
    }
};
</script>

<style>
.table-wrapper {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 40%;
    height: 100vh;
}

.table-container {
    margin-top: 0;
    height: calc(100vh - 20px);
    overflow-y: auto;
    overflow-x: auto;
    flex: 1;
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
    z-index: 1000;
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
</style>
