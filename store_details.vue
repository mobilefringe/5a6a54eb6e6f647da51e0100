<template>
	<div class="page_container store_dets_container" v-if="dataLoaded" id="store_dets_container">
		<div class="page_header" v-if="pageBanner" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">
			<!--http://via.placeholder.com/1920x300-->
			<div class="site_container">
				<div class="header_content">
					<h1>Shopping</h1>
				</div>
			</div>
		</div>
		<div class="site_container">
    		<div class="row">
			<div class="col-sm-4 promo_logo_container hidden_phone">
				<div class="image_container">
					<img v-lazy="currentStore.store.image_url" class="image"/>
				</div>
				<div class="text-center">
				    <h4 v-if="currentStore.store.phone" class="store_dets_title"> {{currentStore.store.phone}}</h4>
				    <h4 v-if="currentStore.store.website" class="store_dets_title"> <a :href="'//'+currentStore.store.website" target="_blank">Store Website</a></h4>
				    <h4 v-if="storeHours " class="store_dets_title"> Store Hours</h4>
				    <ul class="store_hours_list">
                        <li v-if="storeHours" v-for="hour in storeHours">
                            {{hour.day_of_week | moment("dddd", timezone)}} - {{hour.open_time | moment("h A", timezone)}} - {{hour.close_time | moment("h A", timezone)}}
                        </li>
                    </ul>
                    <div class="store_dets_btn caps">
                        <router-link :to="'/stores'+currentStore.store.slug">Store Details & Location</router-link>
                    </div>
				</div>
			</div>
			<div class="col-sm-8 promo_image_container text-left">
			<router-link to="/promotions"><i class="fa fa-angle-left"></i> &nbsp; Back to Promotions</router-link>
			    <h3 class="promo_name" style="margin: 20px auto 0px;">{{currentStore.name}}</h3>
			    <div class="row">
			        <p class="promo_div_date pull-left">{{currentStore.start_date | moment("MMM D", timezone)}} - {{currentStore.end_date | moment("MMM D", timezone)}}</p>
    			    <social-sharing :url="shareURL(currentStore.slug)" :title="currentStore.title" :description="currentStore.body" :quote="_.truncate(currentStore.description, {'length': 99})" twitter-user="EastgateSquare" :media="currentStore.image_url" inline-template >
    					<div class="blog-social-share pull-right" style="margin: 15px auto;">
    						<div class="social_share">
    							<network network="facebook">
    								<i class="fa fa-facebook social_icons" aria-hidden="true"></i>
    							</network>
    							<network network="twitter">
    								<i class="fa fa-twitter social_icons" aria-hidden="true"></i>
    							</network>
    						</div>
    					</div>
    				</social-sharing>
			    </div>
			    
				<div class="col-sm-12 no_padding">
				    <img v-lazy="currentStore.image_url"/>
    				<div class="text-left promo_description">
    				    <p v-html="currentStore.rich_description"></p>
    				</div>
				</div>
				
			</div>
		<!--</div>-->
		<!--<div class="row" style="margin-left:0;">-->
			<!--<div class="col-sm-4 promo_details_container text-left">-->
			<!--	<div>-->
					
			<!--		<p class="promo_store_name">{{currentStore.store.name}}</p>-->
					
			<!--	</div>-->
			<!--</div>-->
			<!--<div class="col-sm-8 promo_desc_container">-->
				
			<!--</div>-->
		</div>
		<div class="promo_promo_container" v-if="storePromos.length > 0">
		    <div class="promo_container_title text-left all_caps"> OTHER {{currentStore.store.name | uppercase }} Promotions</div>
		    <div class="row promo_promo_dets text-left" v-for="promo in storePromos">
		        <div class="col-sm-7" >
		        <div class="promo_div_image">
		            <img v-lazy="promo.image_url" alt=""/>
		        </div>
		        </div>
		        <div class="col-sm-5 promo_div_dets">
		            <p class="promo_div_name">{{promo.name}}</p>
		            <p class="promo_div_promo_name">{{promo.store.name | uppercase}}</p>
		            <p class="promo_div_date">{{promo.start_date | moment("MMM D", timezone)}} - {{promo.end_date | moment("MMM D", timezone)}}</p>
		            <p class="promo_div_description">{{promo.description_short}}</p>
					<span class="feature_read_more">
						<router-link :to="'/promotions/'+promo.slug" class="mobile_readmore" >
							<p class="feature-readmore">Read More <i class="fa fa-chevron-right pull-right" aria-hidden="true"></i></p>
						</router-link>
					</span>
		        </div>
		    </div>
		</div>
		</div>
	</div>
</template>
<script>
    define(['Vue', 'vuex', 'moment', "jquery", "smooth-zoom", "vue!png-map"], function(Vue, Vuex, moment, $, smoothZoom, PNGMapComponent) {
        return Vue.component("store-details-component", {
            template: template, // the variable template will be injected,
            data: function() {
                return {
                    currentStore: null,
                    promotions : [],
                    jobs:[],
                    dataLoaded: false,
                    pageBanner : null 
                }
            },
            props:['id'],
            beforeRouteUpdate(to, from, next) {
                this.currentStore = this.findStoreBySlug(to.params.id);
                if (this.currentStore === null || this.currentStore === undefined){
                    this.$router.replace({ name: '404'});
                }
                next();
            },
            created (){
                this.loadData().then(response => {
                    this.dataLoaded = true;
                    this.updateCurrentStore(this.id);
                    var temp_repo = this.findRepoByName('Stores Banner');
                    if(temp_repo) {
                        this.pageBanner = temp_repo.images[0];
                    }
                    this.pageBanner = this.pageBanner;
                });
            },
            watch: {
                currentStore: function() {
                    if ( _.includes(this.currentStore.store_front_url_abs, 'missing')) {
                        this.currentStore.store_front_url_abs = "//codecloud.cdn.speedyrails.net/sites/5a6a54eb6e6f647da51e0100/image/png/1516652189884/ES_logo_red2.png";
                    }
                    var vm = this;
                    var temp_promo = [];
                    var temp_job = [];
                    _.forEach(this.currentStore.promotions, function(value, key) {
                        var current_promo = vm.findPromoById(value);
                        current_promo.description_short = _.truncate(current_promo.description, {
                            'length': 70
                        });
                        temp_promo.push(current_promo);
                    });
                    _.forEach(this.currentStore.jobs, function(value, key) {
                        var current_job = vm.findJobById(value);
                        current_job.description_short = _.truncate(current_job.description, {
                            'length': 70
                        });
                        temp_job.push(current_job);

                    })
                    this.promotions = temp_promo;
                    this.jobs = temp_job;
                    
                    setTimeout(function() {
                        vm.addLandmark(vm.currentStore);
                    }, 500);
                },
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedStores',
                    'findStoreBySlug',
                    'findPromoById',
                    'findJobById',
                    'findRepoByName'
                ]),
                getPNGurl () {
                    return "https://www.mallmaverick.com" + this.property.map_url;
                },
                svgMapRef() {
                    return _.filter(this.$children, function(o) {
                        return (o.$el.className == "svg-map")
                    })[0];
                },
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData","promotions"), this.$store.dispatch("getData", "jobs"),this.$store.dispatch("getData", "repos")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                updateCurrentStore (id) {
                    this.currentStore = this.findStoreBySlug(id);
                    if (this.currentStore === null || this.currentStore === undefined){
                        this.$router.replace({ name: '404'});
                    }
                },
                updateSVGMap(map) {
                    this.map = map;
                },
                // addLandmark(store) {
                //     this.svgMapRef.addMarker(store);
                // },
            }
        });
    });
</script>