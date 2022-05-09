<template v-if="currentData">
    <tr>
        <td>{{ currentDay}}</td>
        <td>
            <select name="typpp" id="gdsgds" v-model="currentData.price">
                <template v-for="(item, index) in traktamentet" :key="index">
                    <option :value="item">
                        {{ index }} ({{ item }} kr)
                    </option>
                </template>

            </select>
        </td>
        <td>
            <label class="form-container">Breakfast
                <input type="checkbox" name="breakfast" :true-value="0.15" :false-value="0"
                       v-model="currentData.breakfast">
                <span class="checkmark"></span>
            </label>

        </td>
        <td>
            <label class="form-container">Lunch
                <input type="checkbox" name="lunch" :true-value="0.35" :false-value="0" v-model="currentData.lunch">
                <span class="checkmark"></span>
            </label>
        </td>
        <td>
            <label class="form-container">Dinner
                <input type="checkbox" name="dinner" :true-value="0.35" :false-value="0" v-model="currentData.dinner">
                <span class="checkmark"></span>
            </label>
        </td>

        <td><label class="text">{{ totalCost }} kr</label></td>
    </tr>

</template>

<script setup>
    import {ref, computed, watch} from "vue";

    const props = defineProps({
        data: Object,
        traktamentet: Object,
        currentDay: String,
        fullDay: String
    })

    const currentData = ref(props.data)
    const totalCost = computed(() => {
        if (currentData.value.price == 0) {
            return 0;
        }

        let tempFrukost = currentData.value.breakfast !== 0 ? currentData.value.price * currentData.value.breakfast : 0;
        let tempLunchOrDinner = currentData.value.lunch !== 0 ? currentData.value.price * currentData.value.lunch : 0;
        let tempLunchAndDinner = currentData.value.dinner !== 0 ? currentData.value.price * currentData.value.dinner : 0;
        //  let tempAllMeals = currentData.value.allMeals !== 0 ? currentData.value.price * currentData.value.allMeals : 0;
        // 0,35 x 240 kronor

        let total = currentData.value.price - (tempFrukost + tempLunchOrDinner + tempLunchAndDinner);

        return total.toFixed(2)

    })


    watch([props.data], () => {
        if (props.data.price == 0) {
            return 0;
        }

        let tempFrukost = props.data.breakfast !== 0 ? props.data.price * props.data.breakfast : 0;
        let tempLunchOrDinner = currentData.value.lunch !== 0 ? currentData.value.price * currentData.value.lunch : 0;
        let tempLunchAndDinner = currentData.value.dinner !== 0 ? currentData.value.price * currentData.value.dinner : 0;
        //  let tempAllMeals = currentData.value.allMeals !== 0 ? currentData.value.price * currentData.value.allMeals : 0;
        // 0,35 x 240 kronor

        currentData.value.total = currentData.value.price - (tempFrukost + tempLunchOrDinner + tempLunchAndDinner);
    });


</script>

