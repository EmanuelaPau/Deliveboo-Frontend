<script>
import Slider from '../components/Slider.vue';
import { store } from '../store';
import axios from 'axios';
import Loader from '../components/Loader.vue'
export default {
    name: 'AdvanceSearch',
    components: {
        Slider,
        Loader
    },
    data() {
        return {
            loader: true,
            rangeNumber: null,
            search: '',
            store,
            categories: [],
            /**Types Arrays**/
            allTypes: [],
            visibleTypesLarge: [],
            visibleTypesSmall: [],
            visibleTypesXl: [],
            /**Categories Arrays**/
            allCategories: [],
            visibleCategories: [],
            /**Restaurants Arrays**/
            filteredRestaurants: [],
            params: {
                searchbar: '',
                type_ids: [],
                category_ids: []
            }
        }
    },
    methods: {
        getTypes() {
            axios.get(store.ApiUrl + 'types')
                .then((response) => {
                    this.allTypes = response.data.data;
                    this.visibleTypesXl = response.data.data.slice(3, 11);
                    this.visibleTypesLarge = response.data.data.slice(3, 10);
                    this.visibleTypesSmall = response.data.data.slice(3, 8);
                })
                .catch(function (error) {
                    console.log(error);
                });
        },
        getBestRestaurants() {
            axios.get(store.ApiUrl + 'restaurants/bestSeller')
                .then((response) => {
                    store.bestSellers = response.data.data;
                })
                .catch(function (error) {
                    console.log(error);
                });
        },
        getCategories() {
            axios.get(store.ApiUrl + 'categories')
                .then((response) => {
                    this.allCategories = response.data.data;
                    this.visibleCategories = response.data.data.slice(1, 10);
                })
                .catch(function (error) {
                    console.log(error);
                });
        },
        searchRestaurant(typeOrCategory, id) {
            this.params.searchbar = this.search;
            const query = JSON.stringify(this.params);
            const url = store.ApiUrl + 'restaurants/search/advance/' + query;
            axios.get(url)
                .then((response) => {
                    this.filteredRestaurants = response.data.data;
                    console.log(this.filteredRestaurants);
                    this.loader = false;
                })
                .catch((error) => {
                    console.log(error);
                    this.$router.push('/404');
                });
        },
        sidebarCheckboxHandler(typeOrCategory, id) {
            if (typeOrCategory === 'type') {
                if (this.params.type_ids.includes(id)) {
                    let index = this.params.type_ids.indexOf(id);
                    this.params.type_ids.splice(index, 1);
                } else {
                    this.params.type_ids.push(id);
                }
            }
            if (typeOrCategory === 'category') {
                if (this.params.category_ids.includes(id)) {
                    let index = this.params.category_ids.indexOf(id);
                    this.params.category_ids.splice(index, 1);
                } else {
                    this.params.category_ids.push(id);
                }
            }
            console.log(this.params);
        }
    },
    created() {
        this.loader = true;
    },
    mounted() {
        this.getTypes();
        this.getBestRestaurants();
        this.getCategories();
        if (this.$route.params.searchType === 'restaurant') {
            this.search = this.$route.params.searchInput;
            this.searchRestaurant();
        } else if (this.$route.params.searchType === 'type') {
            let type_id = this.$route.params.searchInput;
            this.params.type_ids.push(parseInt(type_id));
            this.searchRestaurant();
        }
    }
}
</script>

<template>
    <!-- Loader  -->
    <Loader v-if="loader === true" />

    <!-- App  -->
    <div v-else>
        <!-- Top filters  -->
        <div class="top-filters ">
            <!-- Searchbar  -->
            <div
                class="select-search d-block d-lg-none d-flex justify-content-center align-items-center flex-column container py-3  ">
                <h1 class="me-3 mb-0 my_search_title">
                    What do you boona eat?
                </h1>
                <div class="search-bar">
                    <label for="home-searchbar">
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 101 101" id="search">
                            <path
                                d="M63.3 59.9c3.8-4.6 6.2-10.5 6.2-17 0-14.6-11.9-26.5-26.5-26.5S16.5 28.3 16.5 42.9 28.4 69.4 43 69.4c6.4 0 12.4-2.3 17-6.2l20.6 20.6c.5.5 1.1.7 1.7.7.6 0 1.2-.2 1.7-.7.9-.9.9-2.5 0-3.4L63.3 59.9zm-20.4 4.7c-12 0-21.7-9.7-21.7-21.7s9.7-21.7 21.7-21.7 21.7 9.7 21.7 21.7-9.7 21.7-21.7 21.7z">
                            </path>
                        </svg>
                    </label>
                    <input type="text" id="home-searchbar" placeholder="Search here" v-model="search"
                        @keyup.enter="searchRestaurant()">
                </div>
            </div>

            <!-- Type filter  -->
            <div class="select-type d-none d-md-block">
                <div class="container d-flex justify-content-center pill-container">
                    <a href="#" v-for="type in visibleTypesXl" class="d-none d-xl-block"
                        @click="sidebarCheckboxHandler('type', type.id); searchRestaurant()">

                        <div class="type_pill d-flex align-items-center"
                            :class="params.type_ids.includes(type.id) ? 'seleceted' : ''">
                            <img class="type-icon" :src="type.logo" alt="">
                            <p class="m-0">{{ type.name }}</p>
                        </div>
                    </a>
                    <a href="#" v-for="type in visibleTypesLarge" class="d-none d-lg-block d-xl-none"
                        @click="sidebarCheckboxHandler('type', type.id); searchRestaurant()">

                        <div class="type_pill d-flex align-items-center"
                            :class="params.type_ids.includes(type.id) ? 'seleceted' : ''">
                            <img class="type-icon" :src="type.logo" alt="">
                            <p class="m-0">{{ type.name }}</p>
                        </div>
                    </a>
                    <a href="#" v-for="type in visibleTypesSmall" class="d-none d-md-block d-lg-none"
                        @click="sidebarCheckboxHandler('type', type.id); searchRestaurant()">

                        <div class="type_pill d-flex align-items-center"
                            :class="params.type_ids.includes(type.id) ? 'seleceted' : ''">
                            <img class="type-icon" :src="type.logo" alt="">
                            <p class="m-0">{{ type.name }}</p>
                        </div>
                    </a>
                </div>
            </div>

            <!-- Category filter  -->
            <div class="select-category d-none d-md-block">
                <div class="container d-flex justify-content-center p-2">
                    <a href="#" class="category-pills" v-for="category in visibleCategories"
                        :class="params.category_ids.includes(category.id) ? 'seleceted' : ''"
                        @click="sidebarCheckboxHandler('category', category.id), searchRestaurant();">
                        <p class="m-0">{{ category.name }}</p>
                    </a>
                </div>
            </div>
        </div>

        <!-- Main app  -->
        <div class=" container-fluid ">
            <div class=" row d-flex search-page-container">
                <!-- Sidebar -->
                <form class="my_sidebar col-3 d-none d-md-block">
                    <!-- <h1>Cerca il tuo ristorante</h1> -->
                    <!-- Searchbar  -->
                    <div class="search-bar mb-4 d-md-none d-lg-flex">
                        <label for="home-searchbar">
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 101 101" id="search">
                                <path
                                    d="M63.3 59.9c3.8-4.6 6.2-10.5 6.2-17 0-14.6-11.9-26.5-26.5-26.5S16.5 28.3 16.5 42.9 28.4 69.4 43 69.4c6.4 0 12.4-2.3 17-6.2l20.6 20.6c.5.5 1.1.7 1.7.7.6 0 1.2-.2 1.7-.7.9-.9.9-2.5 0-3.4L63.3 59.9zm-20.4 4.7c-12 0-21.7-9.7-21.7-21.7s9.7-21.7 21.7-21.7 21.7 9.7 21.7 21.7-9.7 21.7-21.7 21.7z">
                                </path>
                            </svg>
                        </label>
                        <input type="text" id="home-searchbar" placeholder="Search here" v-model="search"
                            @keyup="searchRestaurant()">
                    </div>

                    <!-- Types and Categories-->

                    <div class="accordion accordion-flush " id="">
                        <div class="accordion-item mb-3 accordion-flush">
                            <h2 class="accordion-header my_accordion " id="flush-headingOne">
                                <button class="accordion-button collapsed ps-0" type="button" data-bs-toggle="collapse"
                                    data-bs-target="#flush-collapseOne" aria-expanded="false"
                                    aria-controls="flush-collapseOne">
                                    <h5 class="my_title m-0">Types</h5>
                                </button>
                            </h2>
                            <div id="flush-collapseOne" class="accordion-collapse" aria-labelledby="flush-headingOne">
                                <div class="accordion-body px-0">

                                    <div class="my_select" v-for="type in allTypes">

                                        <input class="my_checkbox" type="checkbox" :id="type.name" name="type"
                                            :value="type.id"
                                            @click="sidebarCheckboxHandler('type', type.id), searchRestaurant()"
                                            :checked="params.type_ids.includes(type.id)">
                                        <label :for="type.name">{{ type.name }}</label>
                                    </div>

                                </div>

                            </div>
                        </div>

                        <!-- Categories  -->

                        <div class="accordion-item mb-5">
                            <h2 class="accordion-header" id="flush-headingTwo">
                                <button class="accordion-button collapsed ps-0" type="button" data-bs-toggle="collapse"
                                    data-bs-target="#flush-collapseTwo" aria-expanded="false"
                                    aria-controls="flush-collapseTwo">
                                    <h5 class="my_title m-0">Categories</h5>
                                </button>
                            </h2>
                            <div id="flush-collapseTwo" class="accordion-collapse " aria-labelledby="flush-headingTwo">
                                <div class="my_select" v-for="category in allCategories">
                                    <input class="my_checkbox" type="checkbox" :id="category.name + 'c'" name="type"
                                        :value="category.id"
                                        @click="sidebarCheckboxHandler('category', category.id), searchRestaurant()"
                                        :checked="params.category_ids.includes(category.id)">
                                    <label :for="category.name + 'c'">{{ category.name }}</label>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Select -  range -->

                    <div class="range-selector mb-5">
                        <div class="d-flex justify-content-between">
                            <h5 class="my_title mb-4">
                                Price
                            </h5>
                            <p>{{ rangeNumber == null ? 1 : rangeNumber }}</p>
                        </div>

                        <div class="input-container d-flex ">

                            <input class="input-range" type="range" min="1" max="100" v-model="rangeNumber" />
                        </div>

                    </div>


                </form>

                <!-- Main -->

                <div class="my_main col-12 col-md-9 mt-4">
                    <div class="container">
                        <div v-if="filteredRestaurants.length === 0" class="row">
                            <!-- Best sellers  -->
                            <div class="col-12 mb-4">
                                <h3 class="cards_title">Our Best Sellers!</h3>
                                <Slider :slides="store.bestSellers" :autoplay="0" :pagination="false" :title="true"
                                    :navigation="true" />
                            </div>
                            <!-- New in town -->
                            <div class="col-12 mb-4">
                                <h3 class="cards_title">New in Town</h3>
                                <Slider :slides="store.bestSellers" :autoplay="0" :pagination="false" :title="true"
                                    :navigation="true" />
                            </div>
                            <!-- Free delivery -->
                            <div class="col-12 mb-4">
                                <h3 class="cards_title">Free delivery under 10 bucks</h3>
                                <Slider :slides="store.bestSellers" :autoplay="0" :pagination="false" :title="true"
                                    :navigation="true" />
                            </div>
                            <!-- Discover more -->
                            <div class="col-12 mb-4">
                                <h3 class="cards_title">Discover more</h3>
                                <div class="my_restaurant_card_container container-fluid">
                                    <div class="row">
                                        <div class="col-sm-12 col-md-6 col-lg-4 mb-4"
                                            v-for="restaurant in store.bestSellers"
                                            @click="this.$router.push({ name: 'RestaurantMenu', params: { id: '0' } })">
                                            <div class=" my_card d-flex justify-content-center align-items-center">
                                                <img class="img-fluid" :src="restaurant.image" alt="">
                                                <h3>{{ restaurant.name }}</h3>
                                            </div>
                                            <div class="my-card-label">
                                                <svg fill="#000000" width="800px" height="800px" viewBox="0 0 100 100"
                                                    xmlns="http://www.w3.org/2000/svg">
                                                    <path
                                                        d="M49,18.92A23.74,23.74,0,0,0,25.27,42.77c0,16.48,17,31.59,22.23,35.59a2.45,2.45,0,0,0,3.12,0c5.24-4.12,22.1-19.11,22.1-35.59A23.74,23.74,0,0,0,49,18.92Zm0,33.71a10,10,0,1,1,10-10A10,10,0,0,1,49,52.63Z" />
                                                </svg>
                                                <h5>
                                                    {{ restaurant.address }}
                                                </h5>
                                            </div>
                                        </div>
                                        <div class="col-12 mt-3 text-end">
                                            <a class="see-more" href="" @click="">See More</a>
                                        </div>
                                    </div>

                                </div>
                            </div>
                        </div>
                        <div v-else class="row">
                            <div class="col-12 mb-4">
                                <h3 class="cards_title">{{ filteredRestaurants.length }} results</h3>
                                <div class="my_restaurant_card_container container-fluid">
                                    <div class="row">
                                        <div class="col-sm-12 col-md-6 col-lg-4 mb-4"
                                            v-for="restaurant in filteredRestaurants"
                                            @click="this.$router.push({ name: 'RestaurantMenu', params: { id: restaurant.id } })">
                                            <div class=" my_card d-flex justify-content-center align-items-center">
                                                <img v-if="restaurant.image" class="img-fluid" :src="restaurant.image.startsWith('/restaurants/') ? (`http://127.0.0.1:8000/storage${restaurant.image}`) : restaurant.image"
                                                    alt="">
                                                <img v-else class="img-fluid"
                                                    src="../assets/mascotte/pattern.jpg" alt="">
                                                <h3>{{ restaurant.name }}</h3>
                                            </div>
                                            <div class="my-card-label">
                                                <svg fill="#000000" width="800px" height="800px" viewBox="0 0 100 100"
                                                    xmlns="http://www.w3.org/2000/svg">
                                                    <path
                                                        d="M49,18.92A23.74,23.74,0,0,0,25.27,42.77c0,16.48,17,31.59,22.23,35.59a2.45,2.45,0,0,0,3.12,0c5.24-4.12,22.1-19.11,22.1-35.59A23.74,23.74,0,0,0,49,18.92Zm0,33.71a10,10,0,1,1,10-10A10,10,0,0,1,49,52.63Z" />
                                                </svg>
                                                <h5>
                                                    {{ restaurant.address }}
                                                </h5>
                                            </div>
                                        </div>
                                        <div class="col-12 mt-3 text-end">
                                            <a class="see-more" href="" @click="">See More</a>
                                        </div>
                                    </div>

                                </div>
                            </div>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
@use '../styles/partials/variables' as *;

.search-bar {
    height: 35px;
    width: 100%;
    margin-top: 1rem;
    display: flex;

    input[type=text] {
        width: 100%;
        height: 100%;
        color: $priGreen;
        // flex-grow: 1;
        padding-left: 0.5rem;
        border-radius: 0 10px 10px 0;
        border: 1px solid #e8ebeb;
        box-shadow: inset 0 2px 4px rgba(0, 0, 0, .05), inset 0 0 0 100px #fff;
    }

    input[type=text]:focus {
        border: 1px solid $secYellow;
    }

    label {
        height: 100%;
        width: 50px;
        aspect-ratio: 1/1;
        cursor: pointer;
        padding: 0 0.3rem;
        background-color: $priGreen;
        border: 1px 0 1px 1px solid $secYellow;
        border: 1px solid rgb(237, 237, 237);
        border-radius: 10px 0 0 10px;


        svg {
            height: 100%;
            aspect-ratio: 1/1;
            fill: $fontWhite;
        }
    }
}



.top-filters {
    background-color: $priGreen;

    .my_search_title {
        color: white;
        font-weight: 700;
    }

    .pill-container {
        column-gap: 1rem;
    }

    .select-type {
        background-color: #3ea27a;
        padding: 1rem 0;

        .type_pill {
            background-color: white;
            height: 60px;
            border-radius: 30px;
            padding: .5rem 1rem;
            transition: all .2s ease-in;

            .type-icon {
                height: 100%;
                aspect-ratio: 1 / 1;
                margin: 0 .7rem 0 0;
            }

        }

        .type_pill:hover {
            transform: scale(1.1);
        }

        .type_pill.seleceted {
            background-color: $priGreen;
            color: $fontWhite;
            border: 1px solid $fontWhite;
        }

        .type_pill.seleceted:hover {
            color: $fontWhite;
            transform: scale(1.05);
        }
    }

    .category-pills {
        background-color: white;
        height: 40px;
        border-radius: 30px;
        padding: .5rem .7rem;
        margin: .5rem .3rem;
        transition: all .2s ease-in;
        border: 1px solid $priGreen;
    }

    .category-pills:hover {
        transform: scale(1.05);
        color: $priGreen;
    }

    .category-pills.seleceted {
        background-color: $priGreen;
        color: $fontWhite;
        border: 1px solid $fontWhite;
    }

    .category-pills.seleceted:hover {
        color: $fontWhite;
        transform: scale(1.05);
    }

}

div.search-page-container {
    // width: 100vw;
    // height: calc(100vh - 95px);

    .my_sidebar {
        padding: 1rem 3rem;
        height: 100%;
        box-shadow: rgba(99, 99, 99, 0.2) 0px 2px 8px 0px;

        .my_title {
            color: $priGreen;
        }


        .accordion-button:not(.collapsed) {
            background-color: transparent
        }

        .my_select {
            color: $detGrey;
            display: flex;
            align-items: center;

            input.my_checkbox {
                margin: .5rem 1rem .5rem 0;
                accent-color: $priGreen;
                width: 20px;
                height: 20px;
            }
        }

        div.range-selector {
            .input-container {
                position: relative;
                width: 100%;

                input {
                    pointer-events: none;
                }

                input[type="range"] {
                    -webkit-appearance: none;
                    appearance: none;
                    background: transparent;
                    cursor: pointer;
                    width: 15rem;
                }

                /***** Track Styles *****/
                /***** Chrome, Safari, Opera, and Edge Chromium *****/
                input[type="range"]::-webkit-slider-runnable-track {
                    position: relative;
                    height: 6px;
                    background-color: rgb(218, 218, 218);
                    border-radius: 6px;
                    pointer-events: auto;
                }

                /******** Firefox ********/
                input[type="range"]::-moz-range-track {
                    position: relative;
                    height: 6px;
                    background-color: rgb(218, 218, 218);
                    border-radius: 6px;
                    pointer-events: auto;
                }

                /***** Thumb Styles *****/
                /***** Chrome, Safari, Opera, and Edge Chromium *****/
                input[type="range"]::-webkit-slider-thumb {
                    -webkit-appearance: none;
                    height: 20px;
                    width: 20px;
                    border-radius: 10px;
                    background-color: $priGreen;
                    border: 1px solid white;
                    position: relative;
                    top: -7px;
                }

                /******** Firefox ********/

                input[type="range"]::-moz-range-thumb {
                    background-color: $priGreen;
                    border: 1px solid white;
                    position: relative;

                }

            }

        }
    }

    .my_main {
        padding: 1rem 2rem;

        .cards_title {
            color: $priGreen;
            font-weight: 700;
            margin-bottom: 1rem;
        }
    }

    .my_restaurant_card_container {
        .my_card {
            position: relative;
            background-color: #3ea27a;
            width: 100%;
            aspect-ratio: 16 / 9;
            border-radius: 10px 10px 0 0;
            padding: 1.5;
            box-shadow: rgba(99, 99, 99, 0.2) 0px 2px 8px 0px;
            color: white;
            text-align: center;
            text-shadow: 0 1px 4px rgba(0, 0, 0, .6);
            overflow: hidden;
            cursor: pointer;

            h3 {
                position: absolute;
                font-size: 2rem;
            }

            img {
                transition: all .3s ease;
            }
        }

        .my_card img:hover {
            transform: scale(1.2);
        }

        .my-card-label {
            width: 100%;
            height: 60px;
            border-radius: 0 0 10px 10px;
            box-shadow: rgba(99, 99, 99, 0.2) 0px 2px 8px 0px;
            color: #878686;
            font-weight: 300;
            display: flex;
            justify-content: center;
            align-items: center;

            svg {
                height: 40px;
                width: 40px;
                fill: #3ea27a;
            }

            h5 {
                margin: 0%;
            }


        }

        a.see-more {
            color: $priGreen;
            transition: all .3s ease;
        }

        a.see-more:hover {
            text-decoration: underline;
        }
    }

}

@media (max-width: $small) {
    h5 {
        font-size: 1.05rem;
    }
}
</style>
