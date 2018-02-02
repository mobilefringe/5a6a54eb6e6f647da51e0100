<template>
	<div v-if="dataloaded">
		<div class="page_header" v-if="promoBanner" v-bind:style="{ backgroundImage: 'url(' + promoBanner.image_url + ')' }">
			<!--http://via.placeholder.com/1920x300-->
			<div class="site_container">
				<div class="header_content">
					<h1>PROMOTIONS</h1>
				</div>
			</div>
		</div>
		<div class="site_container page_content">
			<div id="events_container" v-if="promotions.length > 0">
				<paginate name="promos" v-if="promos" :list="promos" class="paginate-list margin-60" :per="4">
					<div class="row event_container" v-for="(promo,index) in paginated('promos')" :class="{ 'last': index === (paginated('promos').length - 1) }">
						<div class="col-sm-6 col-md-4 event_image_container">
							<router-link :to="'/promotions/'+ promo.slug" class="event_learn_more">
								<img v-lazy="promo.store.image_url"  class="event_image image" alt=""/>
							</router-link>
						</div>
						<div class="col-sm-6 col-md-8 event_dets_container">
							<h4 class="event_name caps">{{promo.name}}</h4>
							<div class="event_thick_line"></div>
							<p class="event_dates">{{promo.start_date | moment("MMM D", timezone)}} - {{promo.end_date | moment("MMM D", timezone)}}</p>
							<p class="event_desc">{{promo.description_short}}</p>
						
							<div class="text-right  col-sm-6" v-if="promo" style="padding:0">
								<router-link :to="'/promotions/'+ promo.slug" class="event_learn_more pull-left">
								    Read More <i class="fa fa-angle-right" aria-hidden="true"></i>
							    </router-link>
								<social-sharing :url="shareURL(promo.slug)" :title="promo.title" :description="promo.body" :quote="_.truncate(promo.description, {'length': 99})" twitter-user="EastgateSquare" :media="promo.image_url" inline-template >
									<div class="blog-social-share pull_right">
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
						</div>
						<div class="col-sm-12">
							<hr>
						</div>
					</div>
				</paginate>
			</div>
			<div id="no_events" class="row" v-else>
				<div class="col-md-12">
					<p>There are no Promotions at this time. Please check back soon.</p>
				</div>
			</div>
			<div class="row margin-60">
				<div class="col-md-12">
					<paginate-links for="promos" :async="true" :limit="5" :show-step-links="true"></paginate-links>
					<!--<paginate-links for="currentSelection" :async="true" :simple="{ next: 'Next »', prev: '« Back' }"></paginate-links>-->
				</div>
			</div>
		</div>
	</div>
</template>

<style>
    .events_container .date_bar{
        /* Today: */
        background: #D3D3D3;
        height: 40px;
        line-height: 40px;
        margin: auto;
        text-align: center;
    }
    .events_container .date_bar .fa{
        cursor: pointer;
    }
    .events_container .current_date{
        color: #636363;
        padding: 0 10px;
    }
    .events_container .all_dates {
        border-bottom: 1px solid #aea99e;
    }
    .events_container .all_dates span {
        font-size: 16px;
        color: #000000;
        letter-spacing: 1.5px;
        height: 30px;
        line-height: 30px;
        padding: 0 5px;
        cursor: pointer;
    }
    .events_container .all_dates [class*="date_"]:focus, [class*="date_"]:hover { 
        background-color: #D3D3D3;
    }
    .events_container .all_dates span.active { 
        background-color: #bababa;
    }
    .events_container .promo_dets {
        border-bottom: 1px solid #aea99e;
    }
    .events_container .row.is-table-row {
        margin: 0;
    }
    .events_container .row.is-table-row [class*="col-"] {
        padding:0;
    }
    .events_container .feature_read_more {
        width : auto;
    }
    .events_container .social_share network {
        display:inline-block;
        width: 24px;
        height: 24px;
        cursor: pointer;
    }
    .events_container .social_share .social_icons{
        width : 24px;
        height : 24px;
        display:inline;
        margin: 0 2px;
    }
    
</style>

<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-meta", "vue-lazy-load"], function(Vue, Vuex, moment, tz, VueMoment, Meta, VueLazyload) {
        Vue.use(Meta);
        Vue.use(VueLazyload);
        return Vue.component("events-component", {
            template: template, // the variable template will be injected
            data () {
              return {
                  dataLoaded: false
              }  
            },
            created () {
                this.$store.dispatch("getData", "events").then(response => {
                   this.dataLoaded = true;
                }, error => {
                    console.error("Could not retrieve data from server. Please check internet connection and try again.");
                    this.$router.replace({ name: '404'});
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedEvents'
                ]),
                events() {
                    var vm = this;
                    var temp_promo = [];
                    var temp_job = [];
                    _.forEach(this.processedEvents, function(value, key) {
                        value.description_short = _.truncate(value.description, {
                            'length': 70
                        });
                        if (value.store != null && value.store != undefined && _.includes(value.store.image_url, 'missing')) {
                            value.store.image_url = "//codecloud.cdn.speedyrails.net/sites/5a6a54eb6e6f647da51e0100/image/png/1516652189884/ES_logo_red2.png";
                        }
                        if (_.includes(value.image_url, 'missing')) {
                            value.image_url = "//codecloud.cdn.speedyrails.net/sites/5a6a54eb6e6f647da51e0100/image/png/1516652189884/ES_logo_red2.png";
                        }
                            
                        temp_promo.push(value);
                    });
                    
                    _.sortBy(temp_promo, [function(o) { return o.start_date; }]);
                    return temp_promo;
                }
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "promotions"), this.$store.dispatch("getData", "repos")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                shareURL(slug){
                    var share_url = "http://eastgatesquare.ca/events/" + slug;
                    return share_url;
                }
            }
        });
    });
</script>