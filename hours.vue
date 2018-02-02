<template>
    <div class="hours_page_container"> <!-- for some reason if you do not put an outer container div this component template will not render -->
        <img style= "width: 100%;" src="//codecloud.cdn.speedyrails.net/sites/5a6a54eb6e6f647da51e0100/image/jpeg/1516038406000/HOURS 2018.jpg" alt="plaza" class="show_phone">  
        <div class="page_container">
            <div class="all_hours_container">
                <div class="row">
                    <div class="col-md-5 col-sm-5 col-xs-12">
                        <h3 class="hours_heading text-left" >Regular Shopping Hours</h3>
                        <div id="hours_container" class="hours_container">
                            <div class="hours_div text-left" v-for="hour in hours">
                                <span>{{hour.day_of_week | moment("dddd", timezone)}}:</span>
                                <span>{{hour.open_time | moment("h:mm A", timezone)}} - {{hour.close_time | moment("h:mm A", timezone)}}</span>
                            </div>
                        </div>
                    </div>
                    <div class="col-md-7 col-sm-7 hidden_phone">
                        <img src="//codecloud.cdn.speedyrails.net/sites/5a6a54eb6e6f647da51e0100/image/jpeg/1516038406000/HOURS 2018.jpg"/>
                    </div> 
                </div>
                <div class="row">
                    <div class="col-md-12 ">
                        <div class="padding_top_20"></div>
                        
                        <h3 class="hours_heading text-left">Extended Holiday Hours</h3>
                        <div id="holidays_hours_container" class="hours_container">
                            <div class="hours_div text-left"  v-for="hour in reducedHolidays">
                                <span>{{hour.holiday_name}} <br/>({{hour.holiday_date | moment("MMM D YYYY", timezone)}})</span>
                                <span>{{hour.open_time | moment("h:mm A", timezone)}} - {{hour.close_time | moment("h:mm A", timezone)}}</span>
                            </div>
                        </div>
                        
                        <div class="padding_top_20"></div>
                        
                        <h3 class="hours_heading text-left">Holiday Closures</h3>
                        <div id="closed_hours_container" class="hours_container">
                            <div class="hours_div text-left" v-for="hour in closeHolidays">
                                <span>{{hour.holiday_name}} ({{hour.holiday_date | moment("MMM D YYYY", timezone)}})</span>
                            </div>
                        </div>
                        
                    </div>
                </div>
                <div class="padding_top_40"></div>
            </div>
        </div >
    </div>
</template>
<style>
    .hours_page_container .row{
        margin-left:inherit;
    }
</style>
<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-meta"], function(Vue, Vuex, moment, tz, VueMoment, Meta) {
        Vue.use(Meta);
        return Vue.component("hours-component", {
            template: template, // the variable template will be injected
            created (){
                this.loadData().then(response => {
                    this.dataloaded = true;
                    this.filteredStores = this.allStores;
                    
                    this.storeBanner = this.findRepoByName('Stores Banner').images[0];
                   console.log(this.storeBanner); 
                });
            },
            
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'getPropertyHours',
                    'getPropertyHolidayHours'
                ]),
                hours () {
                    return this.getPropertyHours;
                },
                holidayHours () {
                    return this.getPropertyHolidayHours;
                },
                reducedHolidays () {
                    var holidayHours = this.holidayHours;
                    return _.filter(holidayHours, function(o) { return !o.is_closed; });
                },
                closeHolidays () {
                    var holidayHours = this.holidayHours;
                    return _.sortBy(_.filter(holidayHours, function(o) { return o.is_closed; }), [function(o) { return o.holiday_date; }]);
                }
            }
        });
    });
</script>
