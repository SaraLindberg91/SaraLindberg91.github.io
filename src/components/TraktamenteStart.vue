<template>
    <div class="container">
        <img alt="Vue logo" src="https://www.visitgroup.com/sites/cb_visit/files/visit-group-pos-logox2.jpeg">


        <div class="section">
            <h3>1. Start by selecting start and end date of your trip</h3>

            <div class="form-wrapper">
                <div>

                    <label for="start" style="display: block">Start date:</label>
                    <input type="date" id="start" name="trip-start" v-model="startDate">
                </div>
                <div>
                    <label for="end" style="display: block">End date:</label>
                    <input type="date" id="end" name="trip-end" :min="startDate" v-model="endDate">
                </div>
            </div>
        </div>



        <div class="section">
            <template v-if="(startDate && endDate) && (endDate >= startDate)">
                <h3>2. Select if you traveled in Sweden or abroad</h3>
                <div class="form-wrapper">
                    <template v-for="c in where" :key="c">
                        <label class="form-container">
                            {{ c }}
                            <input type="radio"
                                   :id="c"
                                   :value="c"
                                   name="where"
                                   v-model="currentWhere">
                            <span class="radiomark"></span>
                        </label>
                    </template>
                </div>
            </template>

        </div>


        <div class="section" v-if="currentWhere == 'Sweden'  && daysArraySweden.length >0">
            <SwedishTraktamente
                    :days-array="daysArraySweden"></SwedishTraktamente>
        </div>
        <div class="section" v-if="currentWhere == 'Abroad' && daysArrayAbroad.length >0">
            <AbroadTraktamente :days-array="daysArrayAbroad"></AbroadTraktamente>
        </div>



    </div>

</template>

<script setup>
    import AbroadTraktamente from './AbroadTraktamente'
    import SwedishTraktamente from './SwedishTraktamente'
    import {ref, watch} from "vue";


//    const countries = ref([]);


    let date = new Date();
    const where = ['Sweden', 'Abroad']
    const currentWhere = ref()

    const formatter = new Intl.DateTimeFormat("sv-se", { // <- re-use me
        year: "numeric",
        month: "2-digit",
        day: "2-digit",
    })

    const startDate = ref(formatter.format(date))
    const endDate = ref(null);
    const daysArraySweden = ref([])
    const daysArrayAbroad = ref([])


    watch([startDate, endDate], () => {
        let start = new Date(startDate.value);
        const end = new Date(endDate.value);
        daysArrayAbroad.value = [];
        daysArraySweden.value = [];

        while (start <= end) {
            let data =
                {
                    date: formatter.format(start),
                    price: 0,
                    breakfast: 0,
                    lunch: 0,
                    dinner: 0,
                    total: 0

                }
            daysArrayAbroad.value.push(data);
            daysArraySweden.value.push(data);
            start.setDate(start.getDate() + 1);
        }
    });


    /*  function numberOfSearchedDays() {
          if (endDate.value && startDate.value) {
              const date1 = new Date(startDate.value);
              const date2 = new Date(endDate.value);
              const diffTime = Math.abs(date2 - date1);

              return Math.ceil(diffTime / (1000 * 60 * 60 * 24))
          }
      }*/


</script>

<style>

</style>