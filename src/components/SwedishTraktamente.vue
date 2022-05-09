<template>
    <h2>Swedish traktamente</h2>

    <table id="chooseTable">
        <thead>
        <tr>
            <th>Datum</th>
            <th>Heldag/ Halvdag</th>
            <th>Breakfast (-48kr)</th>
            <th>Lunch (-84kr)</th>
            <th>Dinner(-84kr)</th>
            <th>Total</th>
        </tr>
        </thead>
        <tbody>

        <TabellSweden :current-day="day.date" :data="day" :daysArray="props.daysArray" :traktamentet="data"
                      v-for="(day, index) in daysArray" :key="index"></TabellSweden>
        </tbody>

        <tfoot>
        <tr>
            <td colspan="6">Total: {{ total }} kr</td>
        </tr>
        </tfoot>
    </table>

    <button class="collapsible" @click="collapseActive  = ! collapseActive">Skatteverket table</button>
    <div class="content" :class="{ 'active' : collapseActive}">
        <table id="skatteverket">
            <thead>
            <tr>
                <th v-for="(col, index) in columns" :key="index">{{col}}</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="row in data" :key="row.id">
                <td v-for="(col, index) in columns" :key="index">{{row[col]}}</td>
            </tr>
            </tbody>
        </table>
    </div>


</template>

<script setup>
    import TabellSweden from './TabellSweden'
    import {ref, onMounted, computed, watch} from "vue";

    const props = defineProps({
        days: Number,
        daysArray: Array
    })


    const data = ref([]);
    /*  const selectedData = ref([]);*/
    const loading = ref(true);
    const error = ref(null);
    const collapseActive = ref(false)
    // const selectedData = ref([]);


    const year = new Date().getFullYear();
    const result = ref(null)
    const total = ref(null)


    const API_URL = `https://skatteverket.entryscape.net/rowstore/dataset/006353ad-aa05-4757-bdfd-fae344433a50`

    let queryParamaters = `?år=${year}`



    const url = `${API_URL}${queryParamaters}`

    const columns = computed(() => {
        if (data.value.length == 0) {
            return [];
        }
        return Object.keys(data.value[0])
    })

    watch([props.daysArray], () => {
        total.value = props.daysArray.reduce(function (total, item) {
            return total + (item.total)
        }, 0);

        /*   return this.$store.getters.tempCart.reduce(function (n, item) {
               return n + (item.BookKey === bookingKey && vue.$store.getters.searchId === item.SearchId);
           }, 0);*/


    });

/*
    const formatter = new Intl.DateTimeFormat("sv-se", { // <- re-use me
        year: "numeric",
        month: "2-digit",
        day: "2-digit",
    })
*/


/*    const dayArraySelected = computed(() => {
        let start = new Date(props.startDate);
        const end = new Date(props.endDate);
        const dates = [];

        while (start <= end) {
            dates.push({
                date: formatter.format(start),
                price: 0,
                breakfast: 0,
                lunch: 0,
                dinner: 0,
                lunchOrDinner: 0,
                lunchAndDinner: 0,
                allMeals: 0
            });
            start.setDate(start.getDate() + 1);

        }
        return dates
    })*/


    function fetchTraktamenteData() {
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
                // set the response data

                //  let gjgjg = json.results;

                data.value = data.value.concat(json.results);

                result.value = json.resultCount


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

        fetchTraktamenteData()
        //  createDayArray()

    });

    /* return { name, data, countries, filterCountiresByName, searchQuery, result };*/
    //},
    //};
</script>

<style>
    .collapsible {
        background-color: #777;
        color: white;
        cursor: pointer;
        padding: 18px;
        width: 100%;
        border: none;
        text-align: left;
        outline: none;
        font-size: 15px;

        margin-top: 5rem;
    }

    .active, .collapsible:hover {
        background-color: #555;
    }

    .collapsible:after {
        content: '\002B';
        color: white;
        font-weight: bold;
        float: right;
        margin-left: 5px;
    }

    .content {
        max-height: 0;
        overflow: hidden;
        transition: max-height 0.2s ease-out;
        background-color: #fff;
    }

    .content.active {
        max-height: none;
        padding: .5rem 0 1rem;
    }
</style>