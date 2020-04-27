<template>
    <div class="page_container" id="contact_us_container"> <!-- for some reason if you do not put an outer container div this component template will not render -->
        <div  v-if="pageBanner" class="page_header" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">
			<div class="site_container">
				<div class="header_content">
					<h1><span>{{$t("newsletter_page.newsletter")}}</span></h1>
				</div>
			</div>
		</div>  
        <div class="site_container">
            <div class="row"> 
                <div class="col-md-12 contact_contents">
                    <div>
                    	<form class="js-cm-form" id="subForm" action="https://www.createsend.com/t/subscribeerror?description=" method="post" data-id="92D4C54F0FEC16E5ADC2B1904DE9ED1A93A125A8390B6A68B1A380E7D07777387FBB6B35406BE6E3B921E42455501130E7A18D31289C34FE379920E1EBBCC8B6">
                    		<div>
                    		    <div class="form-group row">
                        			<div class="col-sm-6 col-xs-12">
                            			<label class="label" for="cm-name">Name </label>
                            			<input aria-label="Name" id="fieldName" maxlength="200" name="cm-name" class="form-control">
                        			</div>
                    			</div>
                    			<div class="form-group row">
                        			<div class="col-sm-6 col-xs-12">
                            			<label class="label" for="cm-fiilhi-fiilhi">Email </label>
                            			<input autocomplete="Email" aria-label="Email" class="form-control js-cm-email-input qa-input-email" id="fieldEmail" maxlength="200" name="cm-fiilhi-fiilhi" required="" type="email">
                        			</div>
                    			</div>
                    			<div>
                    				<div>
                    					<div><label class="checkbox" for="cm-privacy-consent"> <input aria-required="" id="cm-privacy-consent" name="cm-privacy-consent" required="" type="checkbox">I agree to receive Commercial Electronic Messages from Morguard, the management company of {{ property.name }} and its affiliates that will include: special retail sales offers, promotions, new retailer announcements, upcoming events, trend and style news and other inside information. You can unsubscribe at any time. {{ property.name }}’s contact information is as follows: {{ property.address1 }}, {{ property.city }}, {{ property.province_state }}, {{ property.postal_code }}, Attention: Privacy Officer. Website: <a href="https://bonniedoonshoppingcentre.com">bonniedoonshoppingcentre.com.</a> </label></div>
                    					<input id="cm-privacy-consent-hidden" name="cm-privacy-consent-hidden" type="hidden" value="true">
                    				</div>
                    			</div>
                    		</div>
                    		<button type="submit">Subscribe</button>
                    	</form>
                    </div>
                    
                    <!--<form class="form-horizontal" action="https://mobilefringe.createsend.com/t/d/s/fiilhi/" method="post">
                   
                        <div class="form-group ">
                            <div class="col-sm-6 col-xs-12" >
                                <label class="label" for="cm-name">{{$t("newsletter_page.name")}}</label>
                                <input v-model="form_data.name" required class="form-control" name="cm-name" type="text" placeholder="Name">
                            </div>
                        </div>
                        <div class="form-group">
                            <div class="col-sm-6 col-xs-12">
                                <label class="label" for="cm-irudui-irudui">{{$t("newsletter_page.email")}}</label>
                                <input v-model="form_data.email" required class="form-control" name="cm-fiilhi-fiilhi" type="email" placeholder="Email" id="newsletter_email">
                            </div>
                        </div>
                        <div class="form-group">
                            <div class="col-xs-12">
    					        <label class="checkbox">
                                    <input name="agree_newsletter" required  type="checkbox">
                                 
I agree to receive Commercial Electronic Messages from Morguard, the management company of {{ property.name }} and its affiliates that will include: special retail sales offers, promotions, new retailer announcements, upcoming events, trend and style news and other inside information. You can unsubscribe at any time. {{ property.name }}’s contact information is as follows: {{ property.address1 }}, {{ property.city }}, {{ property.province_state }}, {{ property.postal_code }}, Attention: Privacy Officer. Website: <a href="https://bonniedoonshoppingcentre.com">bonniedoonshoppingcentre.com.</a> 
                                </label>
    					    </div>
    					</div>
    					<div class="form-group">
                            <div class="col-xs-12">
                                <button class="contest_btn" type="submit" :disabled="formSuccess">{{$t("newsletter_page.subscribe")}}</button>
                            </div>
                        </div>
                    </form>-->
                    
                    <div id="send_contact_success" class="alert alert-success" role="alert" v-show="formSuccess">
                        <span class="glyphicon glyphicon-ok" aria-hidden="true"></span>
                        <span class="sr-only">{{$t("newsletter_page.success")}} : </span>
                        {{$t("newsletter_page.thank_you_message")}}
                    </div>
                    <div id="send_contact_error" class="alert alert-danger" role="alert" v-show="formError">
                        <span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
                        <span class="sr-only">{{$t("newsletter_page.error")}} : </span>
                        {{$t("newsletter_page.error_message")}}
                    </div>
                    
                </div>
            </div>
            <div class="padding_top_40"></div>    
        </div>
    </div>
</template>
<style>
    .form-group label {
        display: inline-block;
    }
    .checkbox {
        font-weight: normal;
            margin-left: 20px;
    }
</style>
<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-meta", 'vee-validate', 'jquery', 'utility'], function(Vue, Vuex, moment, tz, VueMoment, Meta, VeeValidate, $, Utility) {
        Vue.use(Meta);
        Vue.use(VeeValidate);
        return Vue.component("newsletter-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    currentPage: null,
                    form_data : {},
                    formSuccess : false,
                    formError: false,
                    pageBanner: null
                }
            },
            created () {
                this.loadData().then(response => {
                    var temp_repo = this.findRepoByName('Inside Page Banner');
                    if(temp_repo) {
                        this.pageBanner = temp_repo.images[0];
                    }
                    // this.pageBanner = this.findRepoByName('Contact Us Banner').images[0];
                });    
            },
            mounted () {
                this.form_data.email = this.$route.query.email;
                $("#newsletter_email").val(this.form_data.email);
            },
            watch : {
                $route () {
                    this.form_data.email = this.$route.query.email;
                    $("#newsletter_email").val(this.form_data.email);
                }
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'findRepoByName'
                ])
            },
            methods: {
                validateBeforeSubmit(form) {
                    this.$validator.validateAll().then((result) => {
                        if (result) {
                            let errors = this.errors;
                            
                            if(errors.length > 0) {
                                console.log("Error");
                                this.formError = true;
                            }
                            else {
                                form.preventDefault();
                                console.log("No Error", form);
                                var vm = this;
                                $.getJSON(
                                form.target.action + "?callback=?",
                                $(form.target).serialize(),
                                function (data) {
                                    if (data.Status === 400) {
                                       vm.formError = true;
                                    } else { // 200
                                        vm.formSuccess = true;
                                    }
                                });
                                
                            }
                        }
                    })
                },
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([,this.$store.dispatch("getData", "repos")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
            }
        });
    });
</script>
