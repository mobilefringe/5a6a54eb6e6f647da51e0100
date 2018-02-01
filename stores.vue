<template>
	<div v-if="dataloaded">
		<div class="page_header">
			<div class="site_container">
				<div class="header_content">
					<div class="row margin_90">
						<div class="col-md-12">
							<hr>
							<h1>Shopping</h1>
							<hr>
						</div>
					</div>
					<div class="row bold">
						<div class="col-sm-6 col-md-4">
							<div class="store_search" >
								<label class="">
								<input type="text" placeholder="Find Your Store" id="store_search" />
								<img src="//codecloud.cdn.speedyrails.net/sites/59282acb6e6f647d8d520100/image/png/1496082956000/search_icon.png" class="pull-right" id="store_search_img" alt="">
								<i id="close_search_results" class="fa fa-times-circle hidden_now"></i>
								</label>
								<div class="search_results" id="store_search_results"></div>
							</div>
						</div>
						<div class="col-sm-6 col-md-4">
							<div class="dropdown category_selector">
								<a id="store_cat_list" href="#" data-toggle="dropdown" aria-haspopup="true" aria-expanded="true">Sort By Category <span class="dropdown_arrow"><img src="//codecloud.cdn.speedyrails.net/sites/58bdb9106e6f644783090000/image/png/1489097373000/Expand Arrow.png" alt=""></span></a>
								<ul class="dropdown-menu cat_list" aria-labelledby="dropdownMenu1" id="category_select">
									<script id="category_select_template" type="x-tmpl-mustache/text">
										<li class="show_cat_stores" data-id="{{id}}" name="{{name}}">{{name}}</li>
									</script>
								</ul>
							</div>
						</div>
						<div class="col-md-4 hidden_phone">
							<a class="directory_link" href="/map">
								<div class="promotions_header_container directory_btn">View Map</div>
							</a>
						</div>
					</div>
				</div>
			</div>
		</div>
		<div class="site_container page_content">
			<div class="row">
				<div id="store_list_container">
					<script id="store_list_template" type="x-tmpl-mustache/text">
						<div class="col-xs-6 col-sm-3 col-md-2 cats_row" data-cat="{{cat_list}}">
						    <div class="store_logo_container" id="{{initial}}">
						        <a href="/stores/{{slug}}">
						            <img class="store_img" style="{{initial_img}}" src="{{store_front_url_abs}}"/>
						            <img class="store_hover" style="{{initial_img}}" src="{{hover_img}}"/>
						            <div class="store_coming_soon" style="{{coming_soon_store}}">
						                <div class="new_store">Coming soon</div>
						            </div>
						            <div class="store_list_promos">
						                <span class="promo_exist" style="{{job_exist}}"><img src="//codecloud.cdn.speedyrails.net/sites/58bdb9106e6f644783090000/image/png/1489000358000/jobs.png" class="" alt=""> {{job_list}}</span>
						                <span class="promo_exist" style="{{promotion_exist}}"><img src="//codecloud.cdn.speedyrails.net/sites/58bdb9106e6f644783090000/image/png/1489000371000/promotions.png" class="" alt=""> {{promotion_list}}</span>
						            </div>
						        </a>
						    </div>
						</div>
					</script>
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
        font-family: Helvetica;
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
    /*.category-DD-div .v-select input[type=search] {*/
    /*    display: none;*/
    /*}*/
 /*   #zoom_image{*/
	/*	width:1300px;*/
	/*	height: 787px;*/
	/*	min-width:1300px;*/
	/*	min-height: 787px;*/
	/*	max-width: none;*/
		
	/*}*/
	
	
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
    define(["Vue", "vuex", "vue-select", "jquery", "smooth-zoom", "vue!png-map"], function(Vue, Vuex, VueSelect, $, smoothZoom, PNGMapComponent) {
        return Vue.component("stores-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    listMode: "alphabetical",
                    selectedCat: "All",
                    selectedAlpha: "All",
                    alphabet: ["All", "A", "B", "C", "D", "E", "F", "G", "H", "I", "J", "K", "L", "M", "N", "O", "P", "Q", "R", "S", "T", "U", "V", "W", "X", "Y", "Z"],
                    filteredStores: null,
                    dataloaded: false,
                    mobile_store: false,
                    windowWidth: 0,
                }
            },
            created() {
                // window.Raphael = Raphael; // our mapSvg plugin is stupid and outdated. need this hack to tie Raphael to window object (global variable)
                this.$store.dispatch("getData", "categories").then(response => {
                    this.dataloaded = true;
                    this.filteredStores = this.allStores;
                }, error => {
                    console.error("Could not retrieve data from server. Please check internet connection and try again.");
                });
            },
            watch: {
                windowWidth: function() {
                    if (this.windowWidth <= 768) {
                        this.mobile_store = true;
                    } else {
                        this.mobile_store = false;
                    }
                },
            },
            mounted() {
                // this.filteredStores = this.allStores;
                this.$nextTick(function() {
                    window.addEventListener('resize', this.getWindowWidth);
                    //Init
                    this.getWindowWidth();
                });
            },
            methods: {
                changeMode(mode) {
                    this.listMode = mode;
                },
                updateSVGMap(map) {
                    this.map = map;
                },
                addLandmark(store) {
                    this.svgMapRef.addMarker(store);
                },
                getWindowWidth(event) {
                    this.windowWidth = window.innerWidth;
                },
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedStores',
                    'processedCategories',
                    'storesByAlphaIndex',
                    'storesByCategoryName',
                    'findCategoryById',
                    'findCategoryByName'

                ]),
                allStores() {
                    return this.processedStores;
                },
                allCatergories() {
                    return this.processedCategories;
                },
                dropDownCats() {
                    var cats = _.map(this.processedCategories, 'name');
                    cats.unshift('All');
                    return cats;
                },
                getPNGurl() {
                    return "https://www.mallmaverick.com" + this.property.map_url;
                },
                svgMapRef() {
                    return _.filter(this.$children, function(o) {
                        return (o.$el.className == "svg-map")
                    })[0];
                },
                filterStores() {
                    letter = this.selectedAlpha;
                    if (letter == "All") {
                        this.filteredStores = this.allStores;
                    } else {
                        var filtered = _.filter(this.allStores, function(o, i) {
                            return _.lowerCase(o.name)[0] == _.lowerCase(letter);
                        });
                        this.filteredStores = filtered;
                    }
                },
                filterByCategory() {
                    category_id = this.selectedCat;
                    if (category_id == "All" || category_id == null || category_id == undefined) {
                        category_id = "All";
                    } else {
                        category_id = this.findCategoryByName(category_id).id;
                    }

                    this.breakIntoCol = false;
                    if (category_id == "All") {
                        this.filteredStores = this.allStores;
                    } else {

                        var find = this.findCategoryById;
                        var filtered = _.filter(this.allStores, function(o) {
                            return _.indexOf(o.categories, _.toNumber(category_id)) > -1;
                        });
                    
                        this.filteredStores = filtered;
                    }
                },
            },
            beforeDestroy: function() {
                window.removeEventListener('resize', this.getWindowWidth);
            },
        });
    });
</script>