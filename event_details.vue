<template>
	<div class="promo_dets_container" v-if="currentPromo">
	    <div class="page_header" v-if="promoBanner" v-bind:style="{ backgroundImage: 'url(' + promoBanner.image_url + ')' }">
			<!--http://via.placeholder.com/1920x300-->
			<div class="site_container">
				<div class="header_content">
					<h1>PROMOTIONS</h1>
				</div>
			</div>
		</div>
		<div class="site_container">
    		<div class="row">
			<div class="col-sm-4 promo_logo_container hidden_phone">
				<div class="image_container">
					<img v-lazy="currentPromo.store.image_url" class="image"/>
				</div>
				<div class="text-center">
				    <h4 v-if="currentPromo.store.phone" class="store_dets_title"> {{currentPromo.store.phone}}</h4>
				    <h4 v-if="currentPromo.store.website" class="store_dets_title"> <a :href="'//'+currentPromo.store.website" target="_blank">Store Website</a></h4>
				    <h4 v-if="storeHours " class="store_dets_title"> Store Hours</h4>
				    <ul class="store_hours_list">
                        <li v-if="storeHours" v-for="hour in storeHours">
                            {{hour.day_of_week | moment("dddd", timezone)}} - {{hour.open_time | moment("h A", timezone)}} - {{hour.close_time | moment("h A", timezone)}}
                        </li>
                    </ul>
                    <div class="store_dets_btn caps">
                        <router-link :to="'/stores'+currentPromo.store.slug">Store Details & Location</router-link>
                    </div>
				</div>
			</div>
			<div class="col-sm-8 promo_image_container text-left">
			<router-link to="/promotions"><i class="fa fa-angle-left"></i> &nbsp; Back to Promotions</router-link>
			    <h3 class="promo_name" style="margin: 20px auto 0px;">{{currentPromo.name}}</h3>
			    <div class="row">
			        <p class="promo_div_date pull-left">{{currentPromo.start_date | moment("MMM D", timezone)}} - {{currentPromo.end_date | moment("MMM D", timezone)}}</p>
    			    <social-sharing :url="shareURL(currentPromo.slug)" :title="currentPromo.title" :description="currentPromo.body" :quote="_.truncate(currentPromo.description, {'length': 99})" twitter-user="EastgateSquare" :media="currentPromo.image_url" inline-template >
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
				    <img v-lazy="currentPromo.image_url"/>
    				<div class="text-left promo_description">
    				    <p v-html="currentPromo.rich_description"></p>
    				</div>
				</div>
				
			</div>
		<!--</div>-->
		<!--<div class="row" style="margin-left:0;">-->
			<!--<div class="col-sm-4 promo_details_container text-left">-->
			<!--	<div>-->
					
			<!--		<p class="promo_store_name">{{currentPromo.store.name}}</p>-->
					
			<!--	</div>-->
			<!--</div>-->
			<!--<div class="col-sm-8 promo_desc_container">-->
				
			<!--</div>-->
		</div>
		<div class="promo_promo_container" v-if="storePromos.length > 0">
		    <div class="promo_container_title text-left all_caps"> OTHER {{currentPromo.store.name | uppercase }} Promotions</div>
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
    define(['Vue', 'vuex', 'moment', 'vue-lazy-load'], function(Vue, Vuex, moment, VueLazyload) {
        Vue.use(VueLazyload);
        return Vue.component("event-details-component", {
            template: template, // the variable template will be injected,
            data: function() {
                return {
                    currentPromo: null,
                    storePromos : null,
                    storeHours : null,
                    promoBanner : null
                }
            },
            props:['id'],
            beforeRouteUpdate(to, from, next) {
                this.currentPromo = this.findPEventBySlug(to.params.id);
                    if (this.currentPromo === null || this.currentPromo === undefined){
                        this.$router.replace({ name: '404'});
                    }
                next();
            },
            created(){
                this.loadData().then(response => {
                    this.updateCurrentPromo(this.id);
                    var temp_repo = this.findRepoByName('Promos Banner');
                    if(temp_repo) {
                        this.promoBanner = temp_repo.images[0];
                    }
                    console.log(this.promoBanner);
                    this.promos = this.promotions;
                });
            },
            watch: {
                currentPromo : function (){
                    if(this.currentPromo != null) {
                        console.log(this.currentPromo.store);
                        if (this.currentPromo.store != null && this.currentPromo.store != undefined && _.includes(this.currentPromo.store.image_url, 'missing')) {
                            this.currentPromo.store.image_url = "http://via.placeholder.com/400x400/757575";
                        }
                        else if (this.currentPromo.store == null || this.currentPromo.store == undefined) {
                            this.currentPromo.store = {};
                            this.currentPromo.store.image_url =  "http://via.placeholder.com/400x400/757575";
                        }
                        var vm = this;
                        var temp_promo = [];
                        var current_id =_.toNumber(this.currentPromo.id);
                        _.forEach(this.currentPromo.store.promotions, function(value, key) {
                            if(_.toNumber(value) != current_id){
                                var current_promo = vm.findPromoById(value);
                                current_promo.description_short = _.truncate(current_promo.description, {'length': 70});
                                temp_promo.push(current_promo);
                            }
                        });
                        this.storePromos = temp_promo;
                    }
                    if(this.currentPromo.store) {
                        var storeHours = [];
                        var vm = this;
                        _.forEach(this.currentPromo.store.store_hours, function (value, key) {
                            var hour = vm.findHourById(value);
                            if(hour.day_of_week === 0){
                                hour.order = 7;
                            }
                            else {
                                hour.order = hour.day_of_week;
                            }
                            storeHours.push();
                        });
                        this.storeHours = _.sortBy(storeHours, [function(o) { return o.order; }]);;
                    }
                }
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'processedPromos',
                    'findPromoBySlug',
                    'findPromoById',
                    'timezone',
                    'findRepoByName',
                    'findHourById'
                ]),
                allPromos() {
                    return this.processedPromos;
                },
            },
            methods: {
                updateCurrentPromo (id) {
                    this.currentPromo = this.findPromoBySlug(id);
                    if (this.currentPromo === null || this.currentPromo === undefined){
                        this.$router.replace({ name: '404'});
                    }
                },
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "promotions"), this.$store.dispatch("getData", "repos")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                shareURL(slug){
                    var share_url = "http://mallmaverick.ca/promotions/" + slug;
                    return share_url;
                },
            }
        });
    });
</script>