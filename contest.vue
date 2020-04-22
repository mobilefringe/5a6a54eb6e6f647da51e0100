<template>
    <div class="page_container" id="contact_us_container" v-if="currentContest"> <!-- for some reason if you do not put an outer container div this component template will not render -->
        <div v-if="pageBanner" class="page_header" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">
			<div class="site_container">
				<div class="header_content">
					<h1 v-if="locale=='en-ca'"><span>{{currentContest.name}}</span></h1>
					<h1 v-else><span>{{currentContest.name_2}}</span></h1>
				</div>
			</div>
		</div>
        <div class="site_container">
            <div class="row text-center">
                    <img :src='currentContest.image_url' alt="contest image" class="image"  v-if="locale=='en-ca'">
                    <img :src="'//assets.mallmaverick.com' + currentContest.photo_2_url" alt="contest image" class="image"  v-else>
                </div> 
            <div class="row"> 
                <div class="col-sm-12 contest_contents">
                    <div class="description_text text_left" style="padding-top:40px" v-if="locale=='en-ca'" v-html="currentContest.rich_description"></div>
                    <div class="description_text text_left" style="padding-top:40px" v-else  v-html="currentContest.rich_description_2"></div>
                    <form class="form-horizontal padding_top_20" action="form-submit" v-on:submit.prevent="validateBeforeSubmit">
						<div class="form-group ">
							<div class="col-sm-6 col-xs-12" :class="{'has-error': errors.has('first_name')}">
								<label class="label" for="first_name">First Name</label>
								<input v-model="form_data.first_name" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="first_name" type="text" placeholder="First Name" data-vv-delay="500" data-vv-as="first name">
								<span v-show="errors.has('first_name')" class="form-control-feedback">{{ errors.first('first_name') }}</span>
							</div>
							<div class="col-sm-6 col-xs-12" :class="{'has-error': errors.has('last_name')}">
								<label class="label" for="last_name">Last Name</label>
								<input v-model="form_data.last_name" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="last_name" type="text" placeholder="Last Name" data-vv-delay="500" data-vv-as="last name">
								<span v-show="errors.has('last_name')" class="form-control-feedback">{{ errors.first('last_name') }}</span>
							</div>
						</div>
						<div class="form-group">
							<div class="col-sm-6 col-xs-12" :class="{'has-error': errors.has('email')}">
								<label class="label" for="email">Email</label>
								<input v-model="form_data.email" v-validate="'required|email'" class="form-control" :class="{'input': true}" name="email" type="email" placeholder="Email" data-vv-delay="500" data-vv-as="email">
								<span v-show="errors.has('email')" class="form-control-feedback">{{ errors.first('email') }}</span>
							</div>
							<div class="col-sm-6 col-xs-12" :class="{'has-error': errors.has('phone')}" >
								<label class="label" for="phone">Phone Number</label>
								<input v-model="form_data.phone" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="phone" type="text" placeholder="Phone number" data-vv-delay="500" data-vv-as="phone">
								<span v-show="errors.has('phone')" class="form-control-feedback">{{ errors.first('phone') }}</span>
							</div>
							<div class="col-sm-6 col-xs-12" :class="{'has-error': errors.has('street')}"  style="padding-top: 20px;">
								<label class="label" for="street">Street Address</label>
								<input v-model="form_data.mailing_address" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="street" type="text" placeholder="Street Address" data-vv-delay="500" data-vv-as="street address">
								<span v-show="errors.has('street')" class="form-control-feedback">{{ errors.first('street') }}</span>
							</div>
							<div class="col-sm-6 col-xs-12"  :class="{'has-error': errors.has('city')}" style="padding-top: 20px;">
								<label class="label" for="city"> City</label>
								<input v-model="form_data.city" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="city" type="text" placeholder="Town/City" data-vv-delay="500" data-vv-as="city">
								<span v-show="errors.has('city')" class="form-control-feedback">{{ errors.first('city') }}</span>
							</div>
							<div class="col-sm-6 col-xs-12 " :class="{'has-error': errors.has('postal')}"  style="padding-top: 20px;">
								<label class="label" for="postal">Postal Code</label>
								<input v-model="form_data.postal_code" v-validate="'required:true'" class="form-control" :class="{'input': true}" name="postal" type="text" placeholder="Postal Code" data-vv-delay="500" data-vv-as="postal code">
								<span v-show="errors.has('postal')" class="form-control-feedback">{{ errors.first('postal') }}</span>
							</div>
							<div class="col-sm-6 col-xs-12" :class="{'has-error': errors.has('birthday')}"   style="padding-top: 20px;">
								<label class="label" for="birthday">Birthday</label>
								<input v-model="form_data.birthday" class="form-control" v-validate="'required|date_format:MM/DD/YYYY'" :class="{'input': true}" name="birthday" type="text" placeholder="mm/dd/yyyy" data-vv-delay="500" data-vv-as="birthday">
                                <!--<v-date-picker mode='single' v-model='birthday'  v-validate="'required:true'" select-color='green'></v-date-picker>-->
								<span v-show="errors.has('birthday')" class="form-control-feedback">{{ errors.first('birthday') }}</span>
							</div>
						</div>
						<div class="form-group">
							<div class="col-xs-6" :class="{'has-error': errors.has('validate')}">
								<label class="label" for="validate">Enter the following number below to proceed: {{correctValNum}}</label> 
								<input v-model="validaNum" v-validate="'required|numeric|min:6|max:6'" class="form-control col-xs-3" :class="{'input': true}" name="validate" type="text" placeholder="Enter above number" data-vv-delay="500" data-vv-as="validation">
								<span v-show="errors.has('validate')" class="form-control-feedback">{{ errors.first('validate') }}</span>
								<span v-if="validaNum.length === 6 && validaNum !== correctValNum && errors.first('validate') == null" class="form-control-feedback" :class="{shake_element : validNumError}">Please enter correct number</span>
							</div>
						</div>
						<div class="form-group account-btn text-left m-t-10 agreement">
						    <div class="col-xs-12" :class="{'has-error': errors.has('agree_newsletter')}">
						        <label class="checkbox">
                                    <input name="agree_newsletter" type="checkbox" v-model="form_data.agree_newsletter">
                                        Yes, I would like to receive ongoing news related to events, promotions and special announcements from {{property.name}}
                                </label>
						    </div>
						    <div class="col-xs-12">
						        <label class="checkbox">
                                    <input name="agree_terms" required type="checkbox" >
                                    I agree to the <a href = "/pages/" target="_blank"><u> rules and regulations</u></a>
                                </label>
						    </div>
							<div class="col-xs-12" style="margin-top: 20px;padding: 0;">
								<button class="contest_btn" type="submit" :disabled="formSuccess">Submit</button>
							</div>
						</div>
					</form>
                    
                    <div id="send_contact_success" class="alert alert-success text-left" role="alert" v-show="formSuccess">
                        <span class="glyphicon glyphicon-ok" aria-hidden="true"></span>
                        <span class="sr-only">Success</span>
                        Thank you for contacting us. A member from our team will contact you shortly.
                    </div>
                    <div id="send_contact_error" class="alert alert-danger text-left" role="alert" v-show="formError">
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
    #contact_us_container .row{
        margin-left:inherit;
    }
    .contest_contents {
        min-height: 700px;
    }
    /*.form-group [class*="col-"] {*/
    /*    padding-top:0;    */
    /*}*/
    .form-group .form-control-feedback{
        font-size: 12px;
        /*top:60px;*/
        color: #F44336;
        top: initial;
        bottom: -27px;
    }
    .popover-container input {
        margin-top: 10px;
        display: block;
        width: 100%;
        height: 34px;
        padding: 6px 12px;
        font-size: 14px;
        line-height: 1.42857143;
        color: #555;
        background-color: #fff;
        background-image: none;
        border: 1px solid #ccc;
        border-radius: 4px;
        -webkit-box-shadow: inset 0 1px 1px rgba(0,0,0,.075);
        box-shadow: inset 0 1px 1px rgba(0,0,0,.075);
        -webkit-transition: border-color ease-in-out .15s,-webkit-box-shadow ease-in-out .15s;
        -o-transition: border-color ease-in-out .15s,box-shadow ease-in-out .15s;
        transition: border-color ease-in-out .15s,box-shadow ease-in-out .15s;
    }
    .agreement.form-group {
        margin:0;   
    }
    .checkbox {
        font-weight: normal;
    }
</style>

<script>
var _extends=Object.assign||function(e){for(var t=1;t<arguments.length;t++){var r=arguments[t];for(var a in r)Object.prototype.hasOwnProperty.call(r,a)&&(e[a]=r[a])}return e};define(["Vue","vuex","axios","moment","moment-timezone","vue-moment","vue-meta","vee-validate","v-calendar","utility"],function(t,r,a,n,o,s,c,i,u,m){return t.use(c),t.use(i),t.use(u["default"]),t.component("contest-component",{template:template,props:["locale"],data:function(){return{form_data:{},formSuccess:!1,formError:!1,validaNum:"",correctValNum:null,validNumError:!1,currentContest:null,pageBanner:null}},created:function(){var e=this;this.loadData(this.id).then(function(){e.currentContest=e.findContestBySlug("bonniedoon-test-contest");var t=e.findRepoByName("Inside Page Banner");t&&(e.pageBanner=t.images[0]),e.pageBanner=e.pageBanner})},mounted:function(){this.correctValNum=m.rannumber()},computed:_extends({},r.mapGetters(["property","timezone","findContestBySlug","findRepoByName"])),methods:{validateBeforeSubmit:function(){var t=this;this.$validator.validateAll().then(function(r){if(r&&t.correctValNum===t.validaNum){{t.errors}t.validNumError=!1,t.form_data.agree_newsletter&&$.getJSON("//mobilefringe.createsend.com/t/d/s/bidirr/?callback=?","cm-name="+t.form_data.first_name+t.form_data.last_name+"&cm-bidirr-bidirr="+t.form_data.email+"&cm-f-jtukyu="+t.form_data.city+"&cm-f-jtukjr="+t.form_data.phone+"&cm-f-cjddlt="+t.form_data.mailing_address+"&cm-f-jtukjj="+t.form_data.postal_code+"&cm-f-jtukjt="+t.form_data.birthday,function(t){400===t.Status?(e.preventDefault(),console.error("Please try again later.")):console.log("Newsletter submission successful.")});var a={};a.contest=t.form_data;var n=t;host_name=t.property.mm_host.replace("http:","");var o=host_name+"/contests/"+t.currentContest.slug+"/create_js_entry";$.ajax({url:o,type:"POST",data:a,success:function(){n.formSuccess=!0},error:function(){n.formError=!0}})}})},loadData:function(){var e;return regeneratorRuntime.async(function(t){for(;;)switch(t.prev=t.next){case 0:return t.prev=0,t.next=3,regeneratorRuntime.awrap(Promise.all([this.$store.dispatch("getData","contests"),this.$store.dispatch("getData","repos")]));case 3:return e=t.sent,t.abrupt("return",e);case 7:t.prev=7,t.t0=t["catch"](0),console.log("Error loading data: "+t.t0.message);case 10:case"end":return t.stop()}},null,this,[[0,7]])}}})});
</script>