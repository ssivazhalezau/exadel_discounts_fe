<template>
    <v-container fluid>
        <v-form ref="form">
            <v-row>
                <v-col>
                    <v-list-item-title
                            align="center"
                    ><b>{{$t('dateOfCreatedOfDiscount')}}</b>
                    </v-list-item-title>
                    <date-piker
                            class="mt-16"
                            v-on:selectedDateStart="setStartDate"
                            v-on:selectedDateFinish="setFinishDate"
                    />
                </v-col>
                <v-col>
                    <ChooseOfTown
                            v-on:selectedCountryForObj="setCountry"
                            v-on:selectedCityForObj="setCity"
                    />
                </v-col>
            </v-row>
            <v-btn
                    block
                    @click="clearAll"
            >{{$t('ClearAll')}}
            </v-btn>
            <v-btn class="mt-10"
                   block
                   @click="generate"
            >{{$t('Form')}}
            </v-btn>
            <v-col>

            </v-col>

            <v-card
                    class="mx-auto"
                    tile
            >
                <v-list>
                    <div class="d-flex justify-space-around">
                    <v-list-item-group
                            v-model="selectedItem"
                            color="primary"
                    >
                        <v-list-item
                                v-for="(item, i) in headers()"
                                :key="i"
                        >
                            <v-list-item-content>
                                <v-list-item-title v-text="item.text"></v-list-item-title>
                            </v-list-item-content>
                        </v-list-item>
                    </v-list-item-group>
                    <v-list-item-group
                            v-model="selectedItem"
                            color="primary"
                    >
                        <v-list-item
                                v-for="(item, i) in valuesForText"
                                :key="i"
                        >
                            <v-list-item-content>
                                <v-list-item-title v-text="item"
                                                   :key="componentKey"
                                ></v-list-item-title>
                            </v-list-item-content>
                        </v-list-item>
                    </v-list-item-group>
                    </div>
                </v-list>
            </v-card>
        </v-form>
    </v-container>
</template>
<script>
    import ChooseOfTown from "@/components/ChooseOfTown.vue";
    import token from '@/mixins/token.mixin'
    import DatePiker from "@/components/DatePiker";
    import axios from 'axios'

    const moment = require('moment')

    export default {
        name: "StatisticTotal",
        mixins: [token],
        components: {DatePiker, ChooseOfTown},
        data: () => ({
            selectedItem: 1,
            country: '',
            city: '',
            startDate: '2000-01-01T00:00:00.459Z',
            finishDate: '2100-01-01T00:00:00.459Z',
            valuesForText: [0,0,0,0,0],
            componentKey: 0
        }),
        methods: {
            clearAll() {
                this.$refs.form.reset();
                this.valuesForText[0] = 0
                this.valuesForText[1] = 0
                this.valuesForText[2] = 0
                this.valuesForText[3] = 0
                this.valuesForText[4] = 0
                this.country = '',
                this.city = '',
                this.startDate = '2000-01-01T00:00:00.459Z',
                this.finishDate = '2100-01-01T00:00:00.459Z',
                this.componentKey +=1
            },
            headers() {
                let headerArr = [
                    {text: this.$t('discountsTotal')},
                    {text: this.$t('subscriptionsTotal')},
                    {text: this.$t('favoritesTotal')},
                    {text: this.$t('viewsTotal')},
                    {text: this.$t('ratedTotal')},
                ];
                return headerArr;
            },
            setCountry(country) {
                this.country = country
            },
            setCity(city) {
                this.city = city
            },
            setStartDate(date) {
                this.startDate = `${date}T00:00:00.459Z`
            },
            setFinishDate(date) {
                this.finishDate = `${date}T23:59:59.459Z`
            },
            generate() {
                const getStatistic = () => {
                    let surchObj = {
                        createStartDate: this.startDate,
                        createEndDate: this.finishDate,
                        searchAddressCountry: this.country,
                        searchAddressCity: this.city
                    }
                    axios.post(`${process.env.VUE_APP_URL_SWAGGER}/api/v1/statistics/discounts`, surchObj).then(
                        (data) => {
                            this.valuesForText[0] = data.data.discountsTotal;
                            this.valuesForText[1] = data.data.subscriptionsTotal;
                            this.valuesForText[2] = data.data.favoritesTotal;
                            this.valuesForText[3] = data.data.viewsTotal;
                            this.valuesForText[4] = data.data.ratedTotal;
                            this.componentKey +=1
                        }
                    )
                }
                this.getToken(getStatistic);
            }
        },
      mounted() {
         this.getCountries();
      }
    }
</script>
<style scoped>
</style>
