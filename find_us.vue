<template>
	<div id="find_us_container">
	    <div  v-if="pageBanner" class="page_header" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')', marginBottom:'0px'}">
			<div class="site_container">
				<div class="header_content">
					<h1>Find Us</h1>
				</div>
			</div>
		</div>  
		<!-- for some reason if you do not put an outer container div this component template will not render -->
		<div>
			<div class="row text-left">
					<!--<iframe src="https://www.google.com/maps/embed?pb=!1m14!1m8!1m3!1d1700.7834802639834!2d-79.76570131628588!3d43.230419353327626!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x0%3A0xc2187d635df8645b!2sEastgate+Square+Fortinos!5e0!3m2!1sen!2sca!4v1515083028714" width="100%" height="450" frameborder="0" style="border:0" allowfullscreen></iframe>-->
					<iframe src="https://www.google.com/maps/embed?pb=!1m14!1m8!1m3!1d1405.0774986332171!2d-113.45766032569233!3d53.52063222563502!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x0%3A0xc85d0c9c289d88ad!2sBonnie+Doon+Shopping+Centre!5e0!3m2!1sen!2sca!4v1517707745783" width="100%" height="450" frameborder="0" style="border:0" allowfullscreen></iframe>
					<!--<div class="page_header all_caps double_border_top double_border_bottom">-->
     <!--       			<div class="page_container text_left">How to Get Here</div>-->
     <!--       		</div>-->
					<!--<div  class="directions">-->
					<!--    <p>Eastgate Square is easy to find and there is plenty of parking once you arrive!</p>-->
    	<!--				<p><strong>From the Queen Elizabeth Way (QEW)</strong></p>-->
    	<!--				<p>Simply follow the QEW, take the Centennial Pkwy N. exit, and continue south. We will be on the right hand side of the road.</p>-->
    	<!--				<p><strong>From the Red Hill Expressway</strong></p>-->
    	<!--				<p>Take the Queenston Rd. exit and head eastbound. You will find Eastgate Square on the left hand side, just before the intersection with Centennial Pkwy N.</p>-->
					<!--</div>-->
					
					
					<!--<div class="page_header all_caps double_border_top double_border_bottom">-->
     <!--       			<div class="page_container text_left"> Public Transit</div>-->
     <!--       		</div>-->
     <!--       		<div class="directions">-->
     <!--       		    <p>Eastgate Square is serviced by the Hamilton Street Railway. The following Routes will get you here: 1 (King), B-Line Express, 55 (Stoney Creek Central), 56 (Centennial), and 58 (Stoney Creek Local). Visit <a href="//www.hamilton.ca/CityServices/transit/">www.hamilton.ca/CityServices/transit/</a> for bus maps and schedules.</p>-->
     <!--       		</div>-->
                <div class="text-left site_container padding_tb_30" v-if="currentPage" v-html="currentPage.body"></div>
					
			</div>
			<div class="padding_top_40"></div>
		</div>
	</div>
</template>

<!--<style>-->
<!--    #find_us_container .row{-->
<!--        margin-left:inherit;-->
<!--    }-->
<!--    #find_us_container {-->
<!--        margin: 20px auto 0;-->
<!--    }-->
<!--    #find_us_container .directions {-->
<!--        padding: 10px 0;   -->
<!--    }-->
<!--    .form-group .form-control-feedback{-->
<!--        font-size: 12px;-->
<!--        top:50px;-->
<!--        color: #F44336;-->
<!--    }-->
<!--</style>-->

<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-meta", 'vee-validate'], function(Vue, Vuex, moment, tz, VueMoment, Meta, VeeValidate) {
        Vue.use(Meta);
        Vue.use(VeeValidate);
        return Vue.component("find-us-component", {
            template: template, // the variable template will be injected,
            data: function() {
                return {
                    success_subscribe: false,
                    currentPage: null,
                    pageBanner: null
                }
            },
            created(){
                this.loadData().then(response => {
                    this.currentPage = response[0].data;
                    var temp_repo = this.findRepoByName('Contact Us Banner');
                    if(temp_repo) {
                        this.pageBanner = temp_repo.images[0];
                    }
                    // this.pageBanner = this.findRepoByName('Contact Us Banner').images[0];
                   console.log(this.pageBanner); 
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'findRepoByName'
                ]),
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch('LOAD_PAGE_DATA', {url: this.property.mm_host + "	/pages/bonniedoon-contact-us.json"}),this.$store.dispatch("getData", "repos")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
            }
        });
    });
</script>