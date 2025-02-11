<template>
    <div class="show-hide-container">
        Show/Hide: 
        <span class="eye-icon-container">
            DE
            <font-awesome-icon :icon="visibility.DE ? 'eye' : 'eye-slash'" @click="toggleColumn('DE')" class="eye-icon"/>
        </span>
        <span class="eye-icon-container">
            GR
            <font-awesome-icon :icon="visibility.GR ? 'eye' : 'eye-slash'" @click="toggleColumn('GR')" class="eye-icon"/>
        </span>
        <span class="eye-icon-container">
            FR
            <font-awesome-icon :icon="visibility.FR ? 'eye' : 'eye-slash'" @click="toggleColumn('FR')" class="eye-icon"/>
        </span>
    </div>
</template>

<script>

export default {
    props: {
        visibility: Object
    },
    data() {
        return {
            columns: { ...this.visibility },
        };
    },
    methods: {
        //toggle visibility of column and emit event
        toggleColumn(column) {
            this.columns[column] = !this.visibility[column];
            this.$emit("update:visibility", this.columns);
        },
    }
};
</script>

<style>
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
