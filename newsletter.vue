<template>
    <div class="page_container" id="promotions_container"> <!-- for some reason if you do not put an outer container div this component template will not render -->
        <div  v-if="pageBanner" class="page_header" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">
			<div class="site_container">
				<div class="header_content">
					<h1>Contact Us</h1>
				</div>
			</div>
		</div>  
        <div class="">
            <div class="row">
                <div class="col-md-12 text-center">
                    <img style="margin: 20px auto; max-width:100%;" src="//codecloud.cdn.speedyrails.net/sites/5a6a54eb6e6f647da51e0100/image/jpeg/1516643986000/NEW NEW E News a.jpg" alt="plaza">
                </div> 
            </div>
            <div class="row"> 
                <div class="col-md-12 contact_contents padding_top_20">
                    <form class="form-horizontal" action="//mobilefringe.createsend.com/t/d/s/irudui/" method="post" @submit.prevent="validateBeforeSubmit">
                        <div class="form-group ">
                            <div class="col-sm-6 col-xs-12" >
                                <label class="label" for="cm-name">Name</label>
                                <input v-model="form_data.name" required class="form-control" name="cm-name" type="text" placeholder="Name">
                            </div>
                        </div>
                        <div class="form-group">
                            <div class="col-sm-6 col-xs-12">
                                <label class="label" for="cm-irudui-irudui">Email</label>
                                <input v-model="form_data.email" required class="form-control" name="cm-irudui-irudui" type="email" placeholder="Email" id="newsletter_email">
                            </div>
                        </div>
                        <div class="form-group account-btn text-left m-t-10">
                            <div class="col-xs-12">
    					        <label class="checkbox">
                                    <input name="agree_newsletter" required  type="checkbox">
                                        Yes, I would like to receive ongoing news related to events, promotions and special announcements from Eastgate Square. 
                                </label>
    					    </div>
                            <div class="col-xs-12">
                                <button class="feature-readmore" type="submit" :disabled="formSuccess">Subscribe</button>
                            </div>
                        </div>
                    </form>
                    
                    <div id="send_contact_success" class="alert alert-success" role="alert" v-show="formSuccess">
                        <span class="glyphicon glyphicon-ok" aria-hidden="true"></span>
                        <span class="sr-only">Success</span>
                        Thank you for contacting us. A member from our team will contact you shortly.
                    </div>
                    <div id="send_contact_error" class="alert alert-danger" role="alert" v-show="formError">
                        <span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
                        <span class="sr-only">Error:</span>
                        There was an error when trying to submit your request. Please try again later.
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
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-meta", 'vee-validate', 'jquery'], function(Vue, Vuex, moment, tz, VueMoment, Meta, VeeValidate, $) {
        Vue.use(Meta);
        Vue.use(VeeValidate);
        return Vue.component("newsletter-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    currentPage: null,
                    form_data : {},
                    formSuccess : false,
                    formError: false
                }
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
                ])
            },
            methods: {
                validateBeforeSubmit(form) {
                    this.$validator.validateAll().then((result) => {
                        if (result) {
                            let errors = this.errors;
                            
                            if(errors.length > 0) {
                                console.log("Error");
                            }
                            else {
                                console.log("No Error");
                                // return true;
                                form.target.submit();
                            }
                        }
                    })
                }
            }
        });
    });
</script>
