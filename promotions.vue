<template>
	<div v-if="dataloaded">
		<div class="page_header" v-if="promoBanner" v-lazy:background-image="promoBanner.image_url">
			<!--http://via.placeholder.com/1920x300-->
			<div class="site_container">
				<div class="header_content caps">
					<h1>{{$t("promos_page.promotions")}}</h1>
				</div>
			</div>
		</div>
		<div class="site_container page_content">
			<div id="events_container" v-if="promotions.length > 0">
			
					<div class="row event_container" v-for="(promo,index) in promos" :class="{ 'last': index === promos.length - 1 }" :key="promo.id">
						<div class="col-sm-6 col-md-4 event_image_container">
							<router-link :to="'/promotions/'+ promo.slug" class="event_learn_more"  v-lazy-container="{ selector: 'img' }">
								<img :data-src="promo.image_url"  data-loading='//codecloud.cdn.speedyrails.net/sites/5a8c43eb6e6f641a29020000/image/png/1521035009104/Screen Shot 2018-03-14 at 9.43.24 AM.png' class="event_image image" alt=""/>
							</router-link>
						</div>
						<div class="col-sm-6 col-md-8 event_dets_container">
							<h4 class="event_name caps" v-if="locale=='en-ca'">{{promo.name}}</h4>
							<h4 class="event_name caps" v-else>{{promo.name_2}}</h4>
							<div v-if="promo.promotionable_type == 'Store'">
							    <h4 class="event_store_name caps" v-if="locale=='en-ca'">{{promo.store.name}}</h4>
							    <h4 class="event_store_name caps" v-else>{{promo.store.name_2}}</h4>
							</div>
							
							<div class="event_thick_line"></div>
							<p class="event_dates">{{promo.start_date | moment("MMM D", timezone)}} - {{promo.end_date | moment("MMM D", timezone)}}</p>
							<p class="event_desc"  v-if="locale=='en-ca'" >{{promo.description_short}}</p>
							<p class="event_desc" v-else>{{promo.description_short_2}}</p>
						
							<div class="text-right  col-sm-6" v-if="promo" style="padding:0">
								<router-link :to="'/promotions/'+ promo.slug" class="event_learn_more pull-left">
								   {{$t("promos_page.read_more")}} <i class="fa fa-angle-right" aria-hidden="true"></i>
							    </router-link>
								<social-sharing :url="shareURL(promo.slug)" :title="promo.title" :description="promo.description" :quote="promo.description_short" twitter-user="BonnieDoonSC" :media="promo.image_url" inline-template >
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
			
			</div>
			<div id="no_events" class="row" v-else>
				<div class="col-md-12">
					<p>{{$t("promos_page.no_promo_message")}}</p>
				</div>
			</div>
			<div class="row margin-60">
				<div class="col-md-12">
				
				</div>
			</div>
		</div>
	</div>
</template>

<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-meta", "vue-lazy-load"], function(Vue, Vuex, moment, tz, VueMoment, Meta, VueLazyload) {
        Vue.use(Meta);
        Vue.use(VueLazyload);
        return Vue.component("promos-component", {
            template: template, // the variable template will be injected
            props:['locale'],
            data: function() {
                return {
                    selectedDate: null,
                    filteredPromos:[],
                    dataloaded: false,
                    promoBanner: null,
                    promos : null
                }
            },
            created() {
                this.loadData().then(response => {
                    this.dataloaded = true;
                    
                    var temp_repo = this.findRepoByName('Promos Banner');
                    if(temp_repo) {
                        this.promoBanner = temp_repo.images[0];
                    }
                    // console.log(this.promoBanner);
                    this.promos = this.promotions;
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedPromos',
                    'findRepoByName',
                ]),
                promotions() {
                    var vm = this;
                    var temp_promo = [];
                    var temp_job = [];
                    _.forEach(this.processedPromos, function(value, key) {
                        today = moment().tz(vm.timezone);
                        webDate = moment(value.show_on_web_date).tz(vm.timezone);
                        if (today >= webDate) {
                            value.description_short = _.truncate(value.description, {
                                'length': 150
                            });
                            value.description_short_2 = _.truncate(value.description_2, {
                                'length': 150
                            });
                            if (value.store != null && value.store != undefined && _.includes(value.store.store_front_url_abs, 'missing')) {
                                value.store.store_front_url_abs = "//codecloud.cdn.speedyrails.net/sites/5a8c43eb6e6f641a29020000/image/png/1518554684072/bonniedoonlogo.png";
                            }
                            else if (value.store == null || value.store == undefined) {
                                value.store = {};
                                value.store.store_front_url_abs =  "//codecloud.cdn.speedyrails.net/sites/5a8c43eb6e6f641a29020000/image/png/1518554684072/bonniedoonlogo.png";
                            }
                            temp_promo.push(value);
                        }
                    });
                    console.log(temp_promo.reverse())
                    
                    temp_promo = temp_promo.reverse()
                    
                    _.sortBy(temp_promo, [function(o) { return o.end_date; }]);
                    return temp_promo;
                },
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
                    var share_url = "http://bonniedoonshoppingcentre.com/promotions/" + slug;
                    return share_url;
                },
            }
        });
    });
</script>