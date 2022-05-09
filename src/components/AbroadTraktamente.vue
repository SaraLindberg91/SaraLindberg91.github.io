<template>
    <h3>3. Search country you visited</h3>


    <select name="dropdownCo" id="dropdownCo" v-if="countries && countries.length" v-model="selectedCountryValue"
            @change="setTraktamenteInfo">
        <option value="" disabled selected>Select country</option>
        <option :value="item['normalbelopp']" v-for="(item, index) in filterCountiresByName" :key="index">
            {{ item["land eller område"] }} - {{ item["normalbelopp"]}} kr
        </option>
    </select>


    <div class="section" v-if="selectedCountryValue">
        <h3>4. Fill in your table</h3>
        <table id="chooseTable" style="margin-top: 2rem">
            <thead>
            <tr>
                <th>Datum</th>
                <th>Heldag/ Halvdag</th>
                <th>Breakfast (-15%)</th>
                <th>Lunch (-35%)</th>
                <th>Dinner (-35%)</th>
                <th>Total</th>
            </tr>
            </thead>
            <tbody>

            <TabellAbroad :current-day="day.date" :data="day" :daysArray="props.daysArray"
                          :traktamentet="traktamenteTypes"
                          v-for="(day, index) in daysArray" :key="index"></TabellAbroad>
            </tbody>

            <tfoot>
            <tr>
                <td colspan="6">Total: {{ total }} kr</td>
            </tr>
            </tfoot>
        </table>

    </div>


</template>

<script setup>
    import {ref, onMounted, computed, watch} from "vue";

    import TabellAbroad from "./TabellAbroad"

    const props = defineProps({
        days: Number,
        daysArray: Array

    })

    const API_URL = `https://skatteverket.entryscape.net/rowstore/dataset/70ccea31-b64c-4bf5-84c7-673f04f32505/json`
    const countries = ref([]);
    const loading = ref(true);
    const error = ref(null);
    const selectedCountryValue = ref("")

    const traktamenteTypes = ref({
        'Heldag': null,
        'Halvdag': null
    });


    const year = new Date().getFullYear();
    const result = ref(null)
    const searchQuery = ref('')
    const total = ref(null)

    // a computed ref
    const filterCountiresByName = computed(() => {

        let data = countries.value
        let {filterKey} = ''
        if (searchQuery.value && countries.value) {
            filterKey = searchQuery.value.toLowerCase()
            return data.filter(item => {
                return item['land eller område'].toLowerCase().indexOf(filterKey) > -1
            })
        }
        return data
    })

    watch([props.daysArray], () => {
        total.value = props.daysArray.reduce(function (total, item) {
            return total + (item.total)
        }, 0);

        /*   return this.$store.getters.tempCart.reduce(function (n, item) {
               return n + (item.BookKey === bookingKey && vue.$store.getters.searchId === item.SearchId);
           }, 0);*/


    });


    function setTraktamenteInfo() {
        traktamenteTypes.value['Heldag'] = Number(selectedCountryValue.value)
        traktamenteTypes.value['Halvdag'] = selectedCountryValue.value / 2
    }

    function fetchCountries(url) {
        loading.value = true;

        return fetch(url, {
            method: 'get',
        })
            .then(res => {
                // a non-200 response code

                if (!res.ok) {
                    // create error instance with HTTP status text
                    const error = new Error(res.statusText);
                    error.json = res.json();
                    throw error;
                }

                return res.json();
            })
            .then(json => {
                countries.value = countries.value.concat(json.results);

                result.value = json.resultCount
                if (json.next) {
                    fetchCountries(json.next)
                }

            })
            .catch(err => {
                error.value = err;
                // In case a custom JSON error response was provided
                if (err.json) {
                    return err.json.then(json => {
                        // set the JSON response message
                        error.value.message = json.message;
                    });
                }
            })
            .then(() => {
                loading.value = false;
            });
    }


    onMounted(() => {
        const url = `${API_URL}?år=${year}`
        fetchCountries(encodeURI(url))

    });

</script>

<style scoped>

    input {
        width: 100%;
        padding: 12px 20px;
        margin: 8px 0;
        display: inline-block;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-sizing: border-box;
    }

</style>