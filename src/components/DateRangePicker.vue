<template>
    <div class="date-picker">
        <div class="date-container">
            <label>Start Date:</label>
            <input type="date" :value="startDate" @input="$emit('update:startDate', $event.target.value)"/>
        </div>

        <div>
            <label>End Date:</label>
            <input type="date" :value="endDate" @input="$emit('update:endDate', $event.target.value)"/>
        </div>

    </div>
</template>

<script>
export default {
    props: {
        startDate: String,
        endDate: String,
    }
};
</script>

<style>
.date-picker {
    display: flex;
    flex-direction: column;
    width: 50%;
    gap: 10px;
    margin-bottom: 30px;
    margin-left: 20px;;
    cursor: pointer;
}

.date-picker input {
    padding: 5px;
}

input[type="date"] {
  padding: 8px 12px;
  font-size: 1rem;
  border: 2px solid #49708a;
  border-radius: 8px;
  background-color: #f0f5f9;
  color: #333;
  outline: none;
  transition: all 0.3s ease;
  cursor: pointer;
}

/* Remove default browser styling */
input[type="date"]::-webkit-calendar-picker-indicator {
  filter: invert(0.5);
  cursor: pointer;
}

input[type="date"]:hover {
  border-color: #365d75; 
}

input[type="date"]:focus {
  border-color: #1e4e67;
  box-shadow: 0 0 8px rgba(25, 92, 128, 0.5);
}


</style>
