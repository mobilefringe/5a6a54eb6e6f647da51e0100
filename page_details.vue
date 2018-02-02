<template>
    <div v-if="currentPage">
        <div class="page_header" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">
			<!--http://via.placeholder.com/1920x300-->
			<div class="site_container">
				<div class="header_content">
					<h1>Shopping</h1>
				</div>
			</div>
		</div>
		<div>
		    <div class="page_header all_caps double_border_bottom">
    			<div class="page_container text_left" v-html="currentPage.title"> </div>
    		</div>
            <div class="page_container">
                <div class="row padding_30" >
                    <div class="page_body description_text text_left" v-html="currentPage.body">
                        
                    </div>
                </div>
            </div>
            <div style="padding:20px 0;"></div>
		</div>
    </div>
    <!--Pages Banner-->
</template>
<style>
    .page_title {
        /*border-top:1px solid #aea99e;*/
        border-bottom:1px solid #aea99e;
        height: 35px;
        line-height: 35px;
    }
    #pages_container img{
        width: 100%;
        height: auto;
    }
</style>
<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment"], function(Vue, Vuex, moment, tz, VueMoment) {
        return Vue.component("page-details-component", {
            template: template, // the variable template will be injected,
            data: function() {
                return {
                    success_subscribe: false,
                    currentPage: null,
                    pageBanner : null
                }
            },
            props:['id'],
            beforeRouteUpdate(to, from, next) {
                this.$store.dispatch('LOAD_PAGE_DATA', {
                    url: this.property.mm_host + "/pages/" + to.params.id + ".json"
                }).then(response => {
                    this.currentPage = response.data;
                }, error => {
                    console.error("Could not retrieve data from server. Please check internet connection and try again.");
                    this.$router.replace({
                        name: '404'
                    });
                });
                next();
            },
            created(){
               this.updateCurrentPage(this.id);
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone'
                ])
            },
            methods: {
                updateCurrentPage (id) {
                    this.$store.dispatch('LOAD_PAGE_DATA', {
                        url: this.property.mm_host + "/pages/" + id + ".json"
                    }).then(response => {
                        this.currentPage = response.data;
                    }, error => {
                        console.error("Could not retrieve data from server. Please check internet connection and try again.");
                        this.$router.replace({
                            name: '404'
                        });
                    });
                },
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch('LOAD_PAGE_DATA', {url: this.property.mm_host + "/pages/" + id + ".json"}),this.$store.dispatch("getData", "repos")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
            }
        });
    });
</script>