<template>
	<div class="promo_dets_container page_container" v-if="currentPromo">
		<div class="row">
			<div class="col-sm-4 promo_logo_container hidden_phone">
				<div>
					<img v-lazy="currentPromo.store.image_url"/>
				</div>
			</div>
			<div class="col-sm-8 promo_image_container text-left">
				<img :src="currentPromo.image_url"/>
			</div>
		</div>
		<div class="row" style="margin-left:0;">
			<div class="col-sm-4 promo_details_container text-left">
				<div>
					<h1>{{currentPromo.name}}</h1>
					<p class="promo_store_name">{{currentPromo.store.name}}</p>
					<p class="promo_div_date">{{currentPromo.start_date | moment("MMM D", timezone)}} - {{currentPromo.end_date | moment("MMM D", timezone)}}</p>
				</div>
			</div>
			<div class="col-sm-8 promo_desc_container">
				<div class="text-left promo_description">
				    <p v-html="currentPromo.rich_description"></p>
				</div>
			</div>
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
</template>
<style>
    #promo_dets_container .promo_logo_container,
    #promo_dets_container .promo_image_container,
    #promo_dets_container .promo_details_container,
    #promo_dets_container .promo_desc_container{
        padding: 20px 10px;
    }
    #promo_dets_container .promo_image_container img{
        max-height: 300px;
    }
    #promo_dets_container .promo_logo_container img{
        border: 1px solid #aea99e;
        width:300px;
        height:300px
    }
    #promo_dets_container .promo_details_container h1{
        padding: 0;
    }
    #promo_dets_container .promo_store_name, .promo_div_date{
        font-size: 16px;
    }
    #promo_dets_container .promo_container_title{
        border-top:1px solid #aea99e;
        border-bottom:1px solid #aea99e;
        height: 35px;
        line-height: 35px;
    }
</style>
<script>
    define(['Vue', 'vuex', 'moment', 'vue-lazy-load'], function(Vue, Vuex, moment, VueLazyload) {
        Vue.use(VueLazyload);
        return Vue.component("promo-details-component", {
            template: template, // the variable template will be injected,
            data: function() {
                return {
                    currentPromo: null,
                    storePromos : null
                }
            },
            props:['id'],
            beforeRouteUpdate(to, from, next) {
                this.currentPromo = this.findPromoBySlug(to.params.id);
                    if (this.currentPromo === null || this.currentPromo === undefined){
                        this.$router.replace({ name: '404'});
                    }
                next();
            },
            created(){
                this.$store.dispatch("getData", "promotions").then(response => {
                    this.updateCurrentPromo(this.id);
                }, error => {
                  console.error("Could not retrieve data from server. Please check internet connection and try again.");
                });
            },
            watch: {
                currentPromo : function (){
                    if(this.currentPromo != null) {
                        if (this.currentPromo.store != null && this.currentPromo.store != undefined && _.includes(this.currentPromo.store.image_url, 'missing')) {
                            this.currentPromo.store.image_url = "//codecloud.cdn.speedyrails.net/sites/5a6a54eb6e6f647da51e0100/image/png/1516652189884/ES_logo_red2.png";
                        }
                        else if (this.currentPromo.store == null || this.currentPromo.store == undefined) {
                            this.currentPromo.store = {};
                            this.currentPromo.store.image_url =  ""http://via.placeholder.com/400x400/757575";
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
                }
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'processedPromos',
                    'findPromoBySlug',
                    'findPromoById',
                    'timezone'
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
                }
            }
        });
    });
</script>