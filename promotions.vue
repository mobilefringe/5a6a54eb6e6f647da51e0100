<template>
	<div v-if="dataloaded">
		<div class="page_header" v-bind:style="{ backgroundImage: 'url(' + storeBanner.image_url + ')' }">
			<!--http://via.placeholder.com/1920x300-->
			<div class="site_container">
				<div class="header_content">
					<h1>Shopping</h1>
				</div>
			</div>
		</div>
		<div class="site_container page_content">
			<div class="row margin_30">
                <div class="col-md-6">
                    <h3 class="details_header caps">Events</h3>
                    <div class="thick_line"></div>        
                </div>
            </div>
            <div id="no_events" class="row hidden_now">
                <div class="col-md-12">
                    <p>There are no published Events at this time. Please check back soon.</p>    
                </div>
            </div>
            <div id="events_container">
                <script id="events_template" type="x-tmpl-mustache/text">
                    <div class="row event_container">
                        <div class="col-md-1 hidden_phone">
                            <div class="pull-left">
                                <p class="event_side_date">{{day}}</p>
                                <p class="event_side_day">{{num}}</p>
                                <p class="event_side_date">{{month}}</p>
                            </div>
                        </div>
                        <div class="col-sm-6 col-md-4">
                            <img src="{{image_url}}" class="event_image" alt="">
                        </div>
                        <div class="col-sm-6 col-md-7">
                            <h2 class="event_name">{{name}}</h2> 
                            <div class="event_thick_line"></div>
                            <p class="event_dates" style="{{show_date}}">{{dates}}</p>
                            <p class="event_desc">{{desc_short}}</p> 
                            <a href="/events/{{slug}}" class="event_learn_more">
                                Learn More <i class="fa fa-angle-double-right" aria-hidden="true"></i>
                            </a>
                        </div>
                        <div class="col-sm-12">
                            <hr>
                        </div>
                    </div>
                </script>
                <div id="events_subcontainer">
                    
                </div>
            </div>
            <div id="loadMoreEvents" class="row">
                <div class="col-md-12">
                    <input type="hidden" value="1" id="num_loaded" />
                    <p class="hidden_now" id ="all_loaded">No More Events</p>
                    <p class="" id="loaded_posts">
                        <a href="#" class="red" id="load_more_posts">
                            <span class="post_details_link">Load More</span><i class="fa fa-angle-down" aria-hidden="true"></i>
                        </a>
                    </p>
                </div>
            </div>
		</div>
	</div>
</template>

<style>
    .date_bar{
        /* Today: */
        background: #D3D3D3;
        height: 40px;
        line-height: 40px;
        margin: auto;
        text-align: center;
    }
    .date_bar .fa{
        cursor: pointer;
    }
    .current_date{
        color: #636363;
        padding: 0 10px;
    }
    .all_dates {
        border-bottom: 1px solid #aea99e;
        display: flex;
        flex-wrap: wrap;
    }
    .all_dates span {
        font-size: 16px;
        color: #000000;
        letter-spacing: 1.5px;
        height: 40px;
        line-height: 40px;
        padding: 5px;
        cursor: pointer;
    }
     .all_dates [class*="date_"]:focus, [class*="date_"]:hover { 
        background-color: #D3D3D3;
    }
    .all_dates span.active { 
        background-color: #bababa;
    }
    .promo_dets {
        border-bottom: 1px solid #aea99e;
    }
    .row.is-table-row {
        margin: 0;
    }
    .row.is-table-row [class*="col-"] {
        padding:0;
    }
    .feature_read_more {
        width : auto;
    }
    .social_share span {
        /*display:inline-block;*/
        width: 24px;
        height: 24px;
        cursor: pointer;
    }
    .social_share .social_icons{
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
        return Vue.component("promos-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    selectedDate: null,
                    filteredPromos:[],
                    dataloaded: false
                }
            },
            created() {
                this.$store.dispatch("getData", "promotions").then(response => {
                    this.dataloaded = true;
                    this.selectedDate = moment().tz(this.timezone).format('MMM D, YYYY');
                    var date = moment(this.selectedDate).date();
                    this.daysInMonth[date-1].isActive = true;
                }, error => {
                  console.error("Could not retrieve data from server. Please check internet connection and try again.");
                });
            },
            watch: {
                selectedDate: function() {
                    //sort promos
                    selected = moment(this.selectedDate).format('MM DD YYYY');
                    var vm = this;
                    vm.filteredPromos = _.filter(vm.promotions, function(val){
                        start_date = moment(val.start_date).tz(vm.timezone).format('MM DD YYYY');
                        end_date = moment(val.end_date).tz(vm.timezone).format('MM DD YYYY');
                        return (selected <= end_date && selected >= start_date || selected <= end_date);
                    });
                    //remove old active class, change active class 
                    old_date = moment(this.selectedDate).date();
                    this.daysInMonth.map(date => {
                        date.isActive = false;
                    });
                    this.daysInMonth[old_date-1].isActive = true;
                }
            },
            computed: {
                ...Vuex.mapGetters([
                    'timezone',
                    'processedPromos',
                ]),
                promotions() {
                    var vm = this;
                    var temp_promo = [];
                    var temp_job = [];
                    _.forEach(this.processedPromos, function(value, key) {
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
                },
                daysInMonth() {
                    var days = moment(this.selectedDate).daysInMonth();
                    var all_days= [];
                    for(var i = 1; i <= days; i++) {
                        var temp_day = {};
                        temp_day.date = i;
                        temp_day.isActive = false;
                        
                        all_days.push(temp_day);
                    }
                    return all_days;
                }
            },
            methods: {
                beforeDate: function(ev) {
                    selectedDate = this.selectedDate;
                    selectedDate = moment(selectedDate).subtract(1, 'days');
                    this.selectedDate = moment(selectedDate).tz(this.timezone).format('MMM D, YYYY');
                },
                afterDate() {
                    selectedDate = this.selectedDate;
                    selectedDate = moment(selectedDate).add(1, 'days');
                    this.selectedDate = moment(selectedDate).tz(this.timezone).format('MMM D, YYYY');
                },
                newDate (val) {
                    old_date = moment(this.selectedDate).date();
                    month = moment(this.selectedDate).month();
                    year = moment(this.selectedDate).year();
                    this.selectedDate = moment([year, month, val]).format('MMM D, YYYY');
                },
                shareURL(slug){
                    var share_url = "http://eastgatesquare.ca/promotions/" + slug;
                    return share_url;
                },
            }
        });
    });
</script>