<template>
	<div class="event_dets_container" v-if="currentEvent">
	    <div class="page_header" v-if="eventBanner" v-bind:style="{ backgroundImage: 'url(' + eventBanner.image_url + ')' }">
			<!--http://via.placeholder.com/1920x300-->
			<div class="site_container">
				<div class="header_content caps">
					<h1>EVENTS</h1>
				</div>
			</div>
		</div>
		<div class="site_container">
    		<div class="row">
			<!--<div class="col-sm-4 event_logo_container hidden_phone">-->
			<!--	<div class="image_container">-->
			<!--		<img v-lazy="currentEvent.store.image_url" class="image"/>-->
			<!--	</div>-->
				<!--<div class="text-center">-->
				<!--    <h4 v-if="currentEvent.store.phone" class="store_dets_title"> {{currentEvent.store.phone}}</h4>-->
				<!--    <h4 v-if="currentEvent.store.website" class="store_dets_title"> <a :href="'//'+currentEvent.store.website" target="_blank">Store Website</a></h4>-->
				<!--    <h4 v-if="storeHours " class="store_dets_title"> Store Hours</h4>-->
				<!--    <ul class="store_hours_list">-->
    <!--                    <li v-if="storeHours" v-for="hour in storeHours">-->
    <!--                        {{hour.day_of_week | moment("dddd", timezone)}} - {{hour.open_time | moment("h A", timezone)}} - {{hour.close_time | moment("h A", timezone)}}-->
    <!--                    </li>-->
    <!--                </ul>-->
    <!--                <div class="store_dets_btn caps">-->
    <!--                    <router-link :to="'/stores'+currentEvent.store.slug">Store Details & Location</router-link>-->
    <!--                </div>-->
				<!--</div>-->
			<!--</div>-->
			<div class="col-sm-12 promo_image_container text-left">
			<router-link to="/events"><i class="fa fa-angle-left"></i> &nbsp; Back to Events</router-link>
			    <h3 class="promo_name" style="margin: 20px auto 0px;">{{currentEvent.name}}</h3>
			    <div class="row">
			        <p class="promo_div_date pull-left">{{currentEvent.start_date | moment("MMM D", timezone)}} - {{currentEvent.end_date | moment("MMM D", timezone)}}</p>
    			    <social-sharing :url="shareURL(currentEvent.slug)" :title="currentEvent.title" :description="currentEvent.body" :quote="_.truncate(currentEvent.description, {'length': 99})" twitter-user="EastgateSquare" :media="currentEvent.image_url" inline-template >
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
				    <img v-lazy="currentEvent.image_url" class="image"/>
    				<div class="text-left promo_description">
    				    <p v-html="currentEvent.rich_description"></p>
    				</div>
				</div>
				
			</div>
		<!--</div>-->
		<!--<div class="row" style="margin-left:0;">-->
			<!--<div class="col-sm-4 event_details_container text-left">-->
			<!--	<div>-->
					
			<!--		<p class="event_store_name">{{currentEvent.store.name}}</p>-->
					
			<!--	</div>-->
			<!--</div>-->
			<!--<div class="col-sm-8 event_desc_container">-->
				
			<!--</div>-->
		</div>
		<div class="event_event_container" v-if="storeEvents.length > 0">
		    <div class="event_container_title text-left all_caps"> OTHER {{currentEvent.store.name | uppercase }} Eventtions</div>
		    <div class="row event_event_dets text-left" v-for="event in storeEvents">
		        <div class="col-sm-7" >
		        <div class="event_div_image">
		            <img v-lazy="event.image_url" alt=""/>
		        </div>
		        </div>
		        <div class="col-sm-5 event_div_dets">
		            <p class="event_div_name">{{event.name}}</p>
		            <p class="event_div_event_name">{{event.store.name | uppercase}}</p>
		            <p class="event_div_date">{{event.start_date | moment("MMM D", timezone)}} - {{event.end_date | moment("MMM D", timezone)}}</p>
		            <p class="event_div_description">{{event.description_short}}</p>
					<span class="feature_read_more">
						<router-link :to="'/event/'+event.slug" class="mobile_readmore" >
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
                    currentEvent: null,
                    storeEvents : null,
                    storeHours : null,
                    eventBanner : null
                }
            },
            props:['id'],
            beforeRouteUpdate(to, from, next) {
                this.currentEvent = this.findEventBySlug(to.params.id);
                    if (this.currentEvent === null || this.currentEvent === undefined){
                        this.$router.replace({ name: '404'});
                    }
                next();
            },
            created(){
                this.loadData().then(response => {
                    this.updatecurrentEvent(this.id);
                    var temp_repo = this.findRepoByName('Events Banner');
                    if(temp_repo) {
                        this.eventBanner = temp_repo.images[0];
                    }
                    console.log(this.eventBanner);
                    this.events = this.event;
                });
            },
            watch: {
                currentEvent : function (){
                    if(this.currentEvent != null) {
                        console.log(this.currentEvent.store);
                        if (this.currentEvent.store != null && this.currentEvent.store != undefined && _.includes(this.currentEvent.store.image_url, 'missing')) {
                            this.currentEvent.store.image_url = "http://via.placeholder.com/400x400/757575";
                        }
                        else if (this.currentEvent.store == null || this.currentEvent.store == undefined) {
                            this.currentEvent.store = {};
                            this.currentEvent.store.image_url =  "http://via.placeholder.com/400x400/757575";
                        }
                        var vm = this;
                        var temp_event = [];
                        var current_id =_.toNumber(this.currentEvent.id);
                        _.forEach(this.currentEvent.store.event, function(value, key) {
                            if(_.toNumber(value) != current_id){
                                var current_event = vm.findEventById(value);
                                current_event.description_short = _.truncate(current_event.description, {'length': 70});
                                temp_event.push(current_event);
                            }
                        });
                        this.storeEvents = temp_event;
                    }
                    if(this.currentEvent.store) {
                        var storeHours = [];
                        var vm = this;
                        _.forEach(this.currentEvent.store.store_hours, function (value, key) {
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
                    'processedEents',
                    'findEventBySlug',
                    'findEventById',
                    'timezone',
                    'findRepoByName',
                    'findHourById'
                ]),
                allEvents() {
                    return this.processedEvents;
                },
            },
            methods: {
                updatecurrentEvent (id) {
                    this.currentEvent = this.findEventBySlug(id);
                    if (this.currentEvent === null || this.currentEvent === undefined){
                        this.$router.replace({ name: '404'});
                    }
                },
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "events"), this.$store.dispatch("getData", "repos")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                shareURL(slug){
                    var share_url = "http://mallmaverick.ca/event/" + slug;
                    return share_url;
                },
            }
        });
    });
</script>