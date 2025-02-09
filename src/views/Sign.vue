<template>
    <div>
        <v-container v-if="signFormToggle" class="d-flex align-center wrapper" fluid>
            <v-card width="500" class="mx-auto ">
                <v-card-title :class="{'titleNone' : this.$store.state.userLocation.length === 0 }">
                    {{$t('yourLocationIs')}}
                    {{this.$store.state.userLocation.town}}
                </v-card-title>
                <v-card-text>
                    <ChooseOfTown
                            v-on:selectedCountryForObj="getUserCounty"
                            v-on:selectedCityForObj="getUserCity"
                            class="mt-10"
                    />
                    <v-container
                            class="d-flex justify-center mb-5"
                    >
                        <v-btn @click="this.setUserLocAndLocalStorage"
                               :disabled="this.selectedCountry && this.selectedCity === ''"
                               block
                               color="primary"
                        >
                            {{$t('sLogIn')}}
                        </v-btn>
                    </v-container>
                </v-card-text>
            </v-card>
        </v-container>
        <v-container v-else class="d-flex align-center wrapper" fluid>
            <v-card width="500" class="mx-auto">
                <v-card-text>
                    {{$t('sChooseLocation')}}
                </v-card-text>
                <v-container class="d-flex justify-center align-center">
                    <v-btn @click="backToSelectTown" color="primary">{{$t('sButtonChangeLocation')}}</v-btn>
                </v-container>
                <v-container>
                    <v-row>
                        <v-col cols="12" md="9">
                        </v-col>
                        <v-col cols="12" md="2">
                            <v-select
                                    item-value="ru"
                                    v-model="language"
                                    :items="items"
                            ></v-select>
                        </v-col>
                    </v-row>
                </v-container>
            </v-card>
        </v-container>
    </div>
</template>

<script>
    import AuthService from '@/services/auth.service';
    import {mapMutations} from 'vuex'
    import ChooseOfTown from "@/components/ChooseOfTown";
    import token from '@/mixins/token.mixin'

    const auth = new AuthService();

    export default {
        name: 'Sign',
        components: {ChooseOfTown},
        data: () => ({
            language: sessionStorage.getItem('userLanguage') === "En" ? 'en': 'ru',
            items: ['ru', 'en'],
            selectedCountry: '',
            selectedCity: '',
            signFormToggle: false,
            userClaimsLocalData: [],
        }),
        methods: {
            ...mapMutations(['setUserLocation', 'setLanguage']),
            setUserLocAndLocalStorage() {
                this.setUserLocation({
                    country: this.selectedCountry,
                    town: this.selectedCity
                })

                const userLoc = this.$store.getters.getUserLocation
                localStorage.setItem('key', JSON.stringify(userLoc))
                this.$router.push('/home')
            },
            login() {
                auth.login();
            },
            logout() {
                auth.logout();
            },
            backToSelectTown() {
                this.signFormToggle = true
            },
            getUserCounty(country) {
                this.selectedCountry = country
            },
            getUserCity(city) {
                this.selectedCity = city
            },
        },
        mixins: [token],
        mounted() {
            this.goForAuth(auth);
            this.getCountries();
        },
        watch: {
            $route: {
              immediate: true,
              handler(to) {
                document.title = to.meta.title || 'Discounts';
              }
            },
            language() {
                if (this.language === 'ru') {
                    this.setLanguage(true);
                    sessionStorage.setItem('userLanguage', 'Ru')
                    import(`../langs/ru.json`).then((msg) => {
                        this.$i18n.setLocaleMessage('ru', msg);
                        this.$i18n.locale = 'ru';
                    })
                } else {
                    this.setLanguage(false);
                    sessionStorage.setItem('userLanguage', 'En')
                    import(`../langs/en.json`).then((msg) => {
                        this.$i18n.setLocaleMessage('en', msg);
                        this.$i18n.locale = 'en';
                    })
                }
                this.getCountries()
            }
        },
    };
</script>

<style scoped>
    .wrapper {
        height: 100vh;
        background: #40BDED;
    }
</style>
