<template>
    <div class="date-picker">
        <label>Start Date:</label>
        <input type="date" :value="startDate" @input="$emit('update:startDate', $event.target.value)"/>

        <label>End Date:</label>
        <input type="date" :value="endDate" @input="$emit('update:endDate', $event.target.value)"/>

    </div>
</template>

<script>
export default {
    props: {
        startDate: String,
        endDate: String,
    },
};
</script>

<style>
.date-picker {
    display: flex;
    width: 80%;
    gap: 10px;
    align-items: center;
    margin-bottom: 30px;
    cursor: pointer;
}

.date-picker input {
    padding: 5px;
}

</style>
