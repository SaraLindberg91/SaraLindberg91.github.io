<template v-if="currentData">


    <tr>
        <td>{{ currentDay}}</td>


        <td>
            <select name="typpp" id="gdsgds" v-model="currentData.price">
                <template v-for="(item, index) in traktamentet" :key="index">
                    <option :value="item['i sverige, kr']"
                            v-if="item['vid tjänsteresa'] == 'Halv dag' || item['vid tjänsteresa'] == 'Hel dag'">
                        {{ item['vid tjänsteresa'] }} ({{ item['i sverige, kr'] }} kr)
                    </option>
                </template>

            </select>
        </td>
        <td>
            <label class="form-container">Breakfast
                <input type="checkbox" name="breakfast" :true-value="48" :false-value="0"
                       v-model="currentData.breakfast">
                <span class="checkmark"></span>
            </label>

        </td>
        <td>
            <label class="form-container">Lunch
                <input type="checkbox" name="lunch" :true-value="84" :false-value="0" v-model="currentData.lunch">
                <span class="checkmark"></span>
            </label>
        </td>
        <td>
            <label class="form-container">Dinner
                <input type="checkbox" name="dinner" :true-value="84" :false-value="0" v-model="currentData.dinner">
                <span class="checkmark"></span>
            </label>
        </td>

        <td><label class="form-container">{{ totalCost }} kr</label></td>
    </tr>

</template>

<script setup>
    import {ref, computed, watch} from "vue";

    const props = defineProps({
        data: Object,
        traktamentet: Array,
        currentDay: String
    })

    const currentData = ref(props.data)

    const totalCost = computed(() => {

        if (currentData.value.price == 0) {
            return 0;
        }

        let total = Object.keys(currentData.value).reduce(function () {
            return (currentData.value["breakfast"] + currentData.value["lunch"] + currentData.value["dinner"]);

        }, 0);

        total = Number(currentData.value["price"]) - total

        return total
    })

    watch([props.data], () => {
        if (props.data.price == 0) {
            return 0;
        }

        let total = Object.keys(currentData.value).reduce(function () {
            return (currentData.value["breakfast"] + currentData.value["lunch"] + currentData.value["dinner"]);

        }, 0);

        currentData.value.total = Number(currentData.value["price"]) - total;
    });



</script>
