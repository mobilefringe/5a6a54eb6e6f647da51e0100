<template>
	<div v-if="dataloaded">
		<div class="page_header all_caps double_border_bottom">
			<div class="page_container text_left"> Stores Directory & Map </div>
		</div>
		<div class="page_container">
			<!-- for some reason if you do not put an outer container div this component template will not render -->
			<div class="store-list-left-block col-sm-4 padding_top_20">
		        <div id="mapsvg_store_detail_1" class="show_phone">
					<png-map :png-map-url="getPNGurl" v-bind:initial-position="'500 450'" v-if="mobile_store"></png-map>
				</div>
				<div class="store-list-container">
					<div class="dropdown_container hidden_phone">
						<p class="text_left">Select Category</p>
						<div class="category-DD-div">
							<v-select v-model="selectedCat" :options="dropDownCats" :searchable="false" :on-change="filterByCategory"></v-select>
						</div>
						<p class="text_left" style=" margin-top: 10px;">Sort Alphabetically</p>
						<div class="category-DD-div">
							<v-select v-model="selectedAlpha" :options="alphabet" :searchable="false" :on-change="filterStores"></v-select>
						</div>
					</div>
					<div class="show_phone" style="margin-top:20px">
					    <p class="text_left">Sort By Category :</p>
					    <div class="alphabet-dd  " >
    						<v-select v-model="selectedCat" :options="dropDownCats" :searchable="false" :on-change="filterByCategory" id="mobile_cat_list"></v-select>
    					</div>
					    <p class="text_left">Sort Alphabetically :</p>
    					<div class="alphabet-dd show_phone" >
						    <v-select v-model="selectedAlpha" :options="alphabet" :searchable="false" :on-change="filterStores" id="mobile_alpha_list"></v-select>
					    </div>
					</div>
					
					<ul class="store-listing text_left  padding_top_20">
						<li v-for="store in filteredStores" class="pointer">
							<p class="directory_store_name" v-on:click="addLandmark(store)">{{store.name}}</p>
						</li>
					</ul>
				</div>
			</div>
			<div class="col-sm-8">
				<div id="mapsvg_store_detail" class="padding_top_20">
					<png-map v-bind:png-map-url="getPNGurl" v-bind:initial-position="'500 450'" v-if="!mobile_store"></png-map>
				</div>
			</div>
		</div>
	</div>
</template>

<style>
    .v-select .open-indicator {
        right: 0 !important;
        top: 0 !important;
        bottom: initial !important;
        height: 100% !important;
        padding: 15px 25px 15px 20px !important;
        background-color: #9B9B9B !important;
        border-color: #fff !important;
        color: #fff !important;
    }

    .v-select .open-indicator:before {
        border-color: #fff !important;
        margin-top: -5px;
    }

    .v-select .dropdown-menu {
        font-family: arial;
        font-size: 14px;
        color: #000000;
        letter-spacing: 1.56px;
        display: block;
    }

    .v-select .dropdown-toggle {
        text-align: left;
        border-radius: initial;
    }
    .v-select .selected-tag{
        position:absolute;
    }
	#png_map{
	    width:1300px;
		height: 787px;
		min-width:1300px;
		min-height: 787px;
	}
	
	.text {
	    color:white;
	    font-size:16px;
	    padding-top:2px;
	}

</style>

<script>
    define(["Vue", 'vue-select',jquery", "Raphael", "mm_mapsvg","mousewheel", "vue!svg-map"], function(Vue, VueSelect) {
        return Vue.component("stores-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    listMode: "alphabetical",
                    selectedCat: "All",
                    selectedAlpha: null,
                    alphabet: ["A", "B", "C", "D", "E", "F", "G", "H", "I", "J", "K", "L", "M", "N", "O", "P", "Q", "R", "S", "T", "U", "V", "W", "X", "Y", "Z"],
                    processedStores: null
                }
            },
            created (){
                window.Raphael = Raphael; // our mapSvg plugin is stupid and outdated. need this hack to tie Raphael to window object (global variable)
            },
            mounted() {
                processedStores = this.allStores;
            },
            methods: {
                changeMode(mode) {
                    this.listMode = mode;
                },
                filterByCategory(category_id) {
                    console.dir(JSON.stringify(category_id));
                    if (category_id == "All" || category_id == null || category_id == undefined) {
                        category_id = "All";
                    } else {
                        category_id = this.findCategoryByName(category_id).id;
                    }
                    this.breakIntoCol = false;
                    console.log(category_id);
                    if (category_id == "All") {
                        this.processedStores = this.allStores; //this.storesByAlphaIndex;
                        // this.breakIntoCol = true;
                    } else {
                        var find = this.findCategoryById;
                        var filtered = _.filter(this.allStores, function(o) {
                            return _.indexOf(o.categories, _.toNumber(category_id)) > -1;
                        });
                        // _.forEach(filtered, function(value, i) {
                        //     value.currentCategory = find(category_id).name;
                        // });
                        // // console.log(filtered)
                        // sortedCats = _.groupBy(filtered, store => store.currentCategory);
                        // console.log(sortedCats);
                        this.processedStores = filtered;
                    }
                    // this.processedStores = 
                },
                updateSVGMap (map) {
                    this.map = map;
                },
                dropPin(store) {
                    this.svgMapRef.hideMarkers();
                    console.log(store);
                    this.svgMapRef.addMarker(store,'//codecloud.cdn.speedyrails.net/sites/589e308f6e6f641b9f010000/image/png/1484850466000/show_pin.png');
                    this.svgMapRef.setViewBox(store)
                },
            },
            computed: {
                storesByAlphaIndex() {
                    return this.$store.getters.storesByAlphaIndex;
                },
                storesByCategoryName() {
                    return this.$store.getters.storesByCategoryName;
                },
                allStores() {
                    return this.$store.getters.processedStores;
                },
                allCatergories() {
                    return this.$store.getters.processedCategories;
                },
                dropDownCats() {
                    var cats = _.map(this.$store.getters.processedCategories, 'name');
                    cats.unshift('All');
                    console.log(cats);
                    return cats;
                },
                findCategoryById() {
                    return this.$store.getters.findCategoryById;
                },
                findCategoryByName() {
                    return this.$store.getters.findCategoryByName;
                },
                getSVGurl () {
                    return "https://www.mallmaverick.com" + this.property.svgmap_url;
                    // return "//www.mallmaverick.com/system/site_images/photos/000/035/014/original/Canyon_Crest_-_Map.svg?1512066588";
                },
                svgMapRef() {
                    return _.filter(this.$children, function(o) {
                        return (o.$el.className == "svg-map")
                    })[0];
                }
            }
        });
    });
</script>