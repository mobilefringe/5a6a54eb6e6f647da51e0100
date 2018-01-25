<template>
    <div>
        <!--<transition name="fade">-->
        	<div class="row page_content" v-if="dataLoaded" v-cloak>
        	    <div class="custom_popup" v-if="show_popup">
                    <div class="popup_form_div">
                        <h2 class="pop_up_header p_name">Join Our eClub</h2>
                        <img class="text-left width_100 hide_desk custom_img" src="" />
                        <h3 class="text-right right_desc hide_desk custom_text"></h3>
                        <div class="custom_row">
                            <div class="col-md-6">
                                <section class="">
                                    <form id="form_popup" class="" action="//mobilefringe.createsend.com/t/d/s/irudui/" method="POST">	
                                        <input type="hidden" value="Popup" name="cm-f-tdyudu" />
                                        <input type="hidden" value="" name="cm-name" id="cm-name" v-model="formData.name" />
                                        <label for="FNAME" class="main_label"><span class="">First Name</span>
                                            <input id="FNAME" name="first_name" placeholder="" class="custom_field_control" required="required" type="text" value="" v-model="formData.firstname">
                                        </label>
                                        <label for="LNAME" class="main_label"><span class="">Last Name</span>
                                            <input id="LNAME" name="last_name" placeholder="" class="custom_field_control" required="required" type="text" value=""  v-model="formData.lastname">
                                        </label>
                                        <label for="EMAIL" class="main_label"><span class="">Email Address</span>
                                            <input id="EMAIL" name="cm-irudui-irudui" placeholder="" class="custom_field_control" required="required" type="email" value="">	
                                        </label>
                                        <label for="PCODE" class="main_label"><span class="">Postal Code</span>
                					        <input id="PCODE" name="cm-f-jtukjj" placeholder="" class="custom_field_control" required="required" type="text" value="">
                                        </label>
                                        <label for="FMALEMALE" class="main_label radio_main"><span class="">Female/Male</span>
                                            <label for="FMALEMALE-1" class="radio_inside" required="required">
                                                <input type="radio" name="cm-f-tdyuhl" id="FMALEMALE-1" checked="checked" value="Female">
                                                <span class="">Female</span>
                                            </label>
                                            <label for="FMALEMALE-2" class="radio_inside">
                                                <input type="radio" name="cm-f-tdyuhl" id="FMALEMALE-2" value="Male">
                                                <span class="">Male</span>
                                            </label>
                                        </label>
                                        <label class="main_label">
                                            <input type="checkbox" id="popup_agree" required />
                                            I agree to receive emails from Eastgate Square.
                                        </label>
                                        <button type="submit" class="popup_submit">Submit</button>	
                                    </form>	
                                </section>
                            </div>
                            <div class="col-md-6 hide_phone">
                                <img class="text-left width_100 custom_img" :src="popup.image_url" />
                                <h2 class="text-right right_header p_name">Join Our eClub today!</h2>
                                <h3 class="text-right right_desc custom_text"></h3>
                            </div>
                            <div class="clearfix"></div>
                        </div>
                    </div>
                    <span class="close_popup" @click="closePopup">CLOSE</span>
                </div>
                <div class="page_content">
    <div class="flexslider">
        <ul class="slides" id="home_banner">
            <script id="banner_template" type="x-tmpl-mustache/text">
                <li class="position_relative">
                    <a href="{{url}}" {{css}} onclick="{{noLink}}">
                        <img class="banner_img" alt="banner" src="{{image_url}}" />
                    </a>
                </li>
            </script>
        </ul>
    </div>
        		<div class="banner_div">
        			<div class="main_banner col-sm-9">
        				<div  class="prev"></div>
        				<slick ref="slick" :options="slickOptions">
        					<div class="" v-for="banner in banners" v-if="banners">
            					<!--<div :style="{ 'background-image' : 'url(' + banner.image_url + ')' }" class="banner_image_div">-->
                 <!--                   <router-link v-if="banner.url" :to="banner.url" class="banner_image_li"></router-link>-->
                 <!--                   <router-link v-else to="/" class="banner_image_li"></router-link>-->
                 <!--               </div>-->
        						<img :src="banner.image_url" class="" alt="">
        						<!--<div class="banner_text" v-if="banner.description">-->
        						<!--	<p class="banner-description">{{banner.description}}</p>-->
        						<!--	<router-link :to="banner.url">-->
        						<!--		<div class="banner_see_more">  See All <i class="fa fa-chevron-right pull-right" aria-hidden="true"></i></div>-->
        						<!--	</router-link>-->
        						<!--</div>-->
        					</div>
        				</slick>
        				<div class="next"></div>
        			</div>
        			<div class="top_two hidden_phone col-sm-3">
        				<div class="banner_features"  v-for="feature in banner_features">
        					<router-link :to="feature.url">
        						<img class="top_image" :src='feature.image_url'/>
        						<div class="feature_overlay">{{feature.name}}</div>
        					</router-link>
        				</div>
        			</div>
        		</div>
        		<div style="border-top:4px double #aea99e">
        			<div class="feature-div">
        				<span>
        					<div class="feature-box col-sm-4" v-for="item in feature_items">
        						<p class="feature-title">{{item.name}}</p>
        						<img :src="item.image_url" class="feature-image" />
        						<p class="feature-html">{{item.html}}</p>
        						<!--<p class="feature-desc">{{item.short_description}}</p>-->
        						<span class="feature_read_more">
        							<router-link :to="item.url" class="mobile_readmore" >
        								<p class="feature-readmore">Read More <i class="fa fa-chevron-right pull-right" aria-hidden="true"></i></p>
        							</router-link>
        						</span>
        					</div>
        				</span>
        			</div>
        		</div>
        	</div>
        <!--</transition>-->
    </div>
    
</template>
<!--<div class="page_content">-->
<!--    <div class="flexslider">-->
<!--        <ul class="slides" id="home_banner">-->
<!--            <script id="banner_template" type="x-tmpl-mustache/text">-->
<!--                <li class="position_relative">-->
<!--                    <a href="{{url}}" {{css}} onclick="{{noLink}}">-->
<!--                        <img class="banner_img" alt="banner" src="{{image_url}}" />-->
<!--                    </a>-->
<!--                </li>-->
<!--            </script>-->
<!--        </ul>-->
<!--    </div>-->
<!--    <div class="wide_container">-->
<!--        <div class="margin_30"></div>-->
<!--        <div class="site_container">-->
<!--            <h3 class="home_page_title caps">Explore The Gateway</h3>-->
<!--        </div>-->
<!--        <div class="grid grid_desktop hidden_phone">-->
<!--            <div class="grid-sizer"></div>-->
<!--            <div class="feature_0 grid-item grid-item--width2"></div>-->
<!--            <div class="feature_1 grid-item"></div>-->
            <div class="grid-item"> <!--HOURS-->
<!--                <div class="hours ih-item circle effect19">-->
<!--                    <a href="/hours">-->
<!--                        <img src="http://assets.codecloudapp.com/sites/59282acb6e6f647d8d520100/image/png/1499787583000/gateway_facebook.png" class="" alt="">-->
<!--                        <div class="info" id="home_hours_container_desktop">-->
<!--                            <script id="home_hours_template_desktop" type="x-tmpl-mustache/text">-->
<!--                                <div class="content">-->
<!--                                    <h4 class="bold caps">HOURS</h4>-->
<!--                                    <p class="caps">{{day}}</p>-->
<!--                                    <p>{{hours}}</p>-->
<!--                                    <hr>-->
<!--                                </div>-->
<!--                            </script>-->
<!--                        </div>-->
<!--                    </a>-->
<!--                </div>-->
<!--            </div>-->
<!--            <div class="feature_3 grid-item"></div>-->
<!--            <div class="feature_4 grid-item"></div>-->
<!--            <div class="feature_5 grid-item grid-item--width2"></div>-->
<!--            <div class="feature_6 grid-item grid-item--width2"></div>-->
<!--        </div>-->
<!--        <div class="grid grid_mobile visible_phone">-->
<!--            <div class="grid-sizer"></div>-->
<!--            <div class="feature_0 grid-item grid-item--width2"></div>-->
<!--            <div class="feature_1 grid-item"></div>-->
            <div class="grid-item"> <!--HOURS MOBILE-->
<!--                <div class="hours ih-item circle effect19">-->
<!--                    <a href="/hours">-->
<!--                        <img src="http://assets.codecloudapp.com/sites/59282acb6e6f647d8d520100/image/png/1499787583000/gateway_facebook.png" class="" alt="">-->
<!--                        <div class="info" id="home_hours_container_mobile">-->
<!--                            <script id="home_hours_template_mobile" type="x-tmpl-mustache/text">-->
<!--                                <div class="content">-->
<!--                                    <h4 class="bold caps">HOURS</h4>-->
<!--                                    <p class="caps">{{day}}</p>-->
<!--                                    <p>{{hours}}</p>-->
<!--                                    <hr>-->
<!--                                </div>-->
<!--                            </script>-->
<!--                        </div>-->
<!--                    </a>-->
<!--                </div>-->
<!--            </div>-->
<!--            <div class="feature_3 grid-item"></div>-->
<!--            <div class="feature_4 grid-item"></div>-->
<!--            <div class="feature_5 grid-item grid-item--width2"></div>-->
<!--            <div class="feature_6 grid-item grid-item--width2"></div>-->
<!--        </div>-->
<!--        <div class="margin_30"></div>-->
        
<!--        <div class="site_container">-->
<!--            <h3 class="home_page_title caps">What's Happening At The Gateway</h3>-->
<!--        </div>-->
<!--        <div class="grid grid_desktop hidden_phone">-->
<!--            <div class="grid-sizer"></div>-->
<!--            <div class="feature_7 grid-item grid-item--height2"></div>-->
<!--            <div class="feature_8 grid-item grid-item--width2"></div>-->
            <div class="grid-item"> <!--INSTAGRAM-->
<!--                <div class="social ih-item circle effect19">-->
<!--                    <a href="https://www.instagram.com/shopthegateway/">-->
<!--                        <div class="instafeed_single"></div>-->
<!--                        <div class="info">-->
<!--                            <div class="content">-->
<!--                                <i class="fa fa-instagram hover_icon" aria-hidden="true"></i>-->
<!--                                <h3>@SHOPGATEWAY</h3>-->
<!--                            </div>-->
<!--                        </div>-->
<!--                    </a>-->
<!--                </div>-->
<!--            </div>-->
<!--            <div class="feature_9 grid-item"></div>-->
            <div class="grid-item"> <!--FACEBOOK-->
<!--                <div class="social ih-item circle effect19">-->
<!--                    <a href="https://www.facebook.com/shopthegateway/">-->
<!--                        <img src="//codecloud.cdn.speedyrails.net/sites/59282acb6e6f647d8d520100/image/jpeg/1499977125000/12.jpg" alt="Facebook"/>-->
<!--                        <div class="info">-->
<!--                            <div class="content">-->
<!--                                <i class="fa fa-facebook hover_icon" aria-hidden="true"></i>-->
<!--                                <h3>SHOPGATEWAY</h3>-->
<!--                            </div>-->
<!--                        </div>-->
<!--                    </a>-->
<!--                </div>-->
<!--            </div>-->
<!--            <div class="feature_10 grid-item grid-item--width2"></div>-->
            <div class="grid-item"> <!--SnapChat-->
<!--                <div class="snapchat social ih-item circle effect19">-->
<!--                    <a href="#" data-toggle="modal" data-target="#snap_modal">-->
<!--                        <img src="//codecloud.cdn.speedyrails.net/sites/59282acb6e6f647d8d520100/image/jpeg/1499977122000/10.jpg" alt="SnapChat"/>-->
<!--                        <div class="info">-->
<!--                            <div class="content">-->
<!--                                <i class="fa fa-snapchat-ghost hover_icon" aria-hidden="true"></i>-->
<!--                                <h3>@ShopTheGateway</h3>-->
<!--                            </div>-->
<!--                        </div>-->
<!--                    </a>-->
<!--                </div>    -->
<!--            </div>-->
<!--        </div>-->
<!--        <div class="grid grid_mobile visible_phone">-->
<!--            <div class="grid-sizer"></div>-->
<!--            <div class="feature_8 grid-item grid-item--width2"></div>-->
<!--            <div class="feature_7 grid-item grid-item--height2"></div>-->
<!--            <div class="feature_9 grid-item"></div>-->
            <div class="grid-item"> <!--SnapChat-->
<!--                <div class="snapchat social ih-item circle effect19">-->
<!--                    <a href="#" data-toggle="modal" data-target="#snap_modal">-->
<!--                        <img src="//codecloud.cdn.speedyrails.net/sites/59282acb6e6f647d8d520100/image/jpeg/1499977122000/10.jpg" alt="SnapChat"/>-->
<!--                        <div class="info">-->
<!--                            <div class="content">-->
<!--                                <i class="fa fa-snapchat-ghost hover_icon" aria-hidden="true"></i>-->
<!--                                <h3>@ShopTheGateway</h3>-->
<!--                            </div>-->
<!--                        </div>-->
<!--                    </a>-->
<!--                </div>    -->
<!--            </div>-->
<!--            <div class="feature_10 grid-item grid-item--width2"></div>-->
            <div class="grid-item"> <!--INSTAGRAM-->
<!--                <div class="social ih-item circle effect19">-->
<!--                    <a href="https://www.instagram.com/shopthegateway/">-->
<!--                        <div class="instafeed_single"></div>-->
<!--                        <div class="info">-->
<!--                            <div class="content">-->
<!--                                <i class="fa fa-instagram hover_icon" aria-hidden="true"></i>-->
<!--                                <h3>@SHOPGATEWAY</h3>-->
<!--                            </div>-->
<!--                        </div>-->
<!--                    </a>-->
<!--                </div>-->
<!--            </div>-->
            <div class="grid-item"> <!--FACEBOOK-->
<!--                <div class="social ih-item circle effect19">-->
<!--                    <a href="https://www.facebook.com/shopthegateway/">-->
<!--                        <img src="//codecloud.cdn.speedyrails.net/sites/59282acb6e6f647d8d520100/image/jpeg/1499977125000/12.jpg" alt="Facebook"/>-->
<!--                        <div class="info">-->
<!--                            <div class="content">-->
<!--                                <i class="fa fa-facebook hover_icon" aria-hidden="true"></i>-->
<!--                                <h3>SHOPGATEWAY</h3>-->
<!--                            </div>-->
<!--                        </div>-->
<!--                    </a>-->
<!--                </div>-->
<!--            </div>-->
<!--        </div>-->
<!--        <div class="margin_30"></div>-->
        
<!--        <div class="site_container">-->
<!--            <h3 class="home_page_title caps">In Our Feed</h3>-->
<!--        </div>-->
<!--        <div id="instafeed"></div>-->
<!--        <div class="margin_60"></div>-->
<!--    </div>-->
<!--</div>-->
<script>
    define(["Vue", "vuex", "vue!today_hours", "vue!search-component", 'vue!vue-slick','js-cookie'], function(Vue, Vuex, TodayHoursComponent, SearchComponent, slick, Cookies) {
        return Vue.component("home-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    suggestionAttribute: 'name',
                    search: '',
                    slickOptions: {
                        slidesToShow: 1,
                        dots : true,
                        arrows:true,
                        prevArrow: '.prev',
                        nextArrow: '.next',
                        autoplay: false
                        // Any other options that can be got from plugin documentation
                    },
                    dataLoaded: false,
                    show_popup: false,
                    popup: null,
                    formData : {}
                }
            },
            created () {
                this.loadData().then(response => {
                    this.dataLoaded = true;
                    this.popup = this.$store.state.popups[0];
                });
            },
            watch : {
                dataLoaded () {
                    var viewed = Cookies.get('popup_viewed');
                    if(this.popup !== null && viewed !== "true") {
                        Cookies.set('popup_viewed', "true");
                        viewed = Cookies.get('popup_viewed');
                        this.show_popup = true;
                        this.popup.image_url = "//mallmaverick.cdn.speedyrails.net" + this.popup.photo_url;
                        document.getElementById('popup_backdrop').style.display = "block";
                    }
                    else {
                        document.getElementById('popup_backdrop').style.display = "none";
                    }
                },
                formData () {
                    this.formData.name = this.formData.firstname + " " + this.formData.lastname; 
                }
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedStores'
                ]),
                banners () {
                    return _.orderBy(this.$store.state.banners, ['position'], ['asc']);
                },
                banner_features () {
                    return  _.slice(this.$store.state.feature_items, 0, 2);
                },
                feature_items () {
                    return _.slice(this.$store.state.feature_items, 2,this.$store.state.feature_items.length );
                }
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "banners"), this.$store.dispatch("getData", "feature_items"), this.$store.dispatch("getData", "promotions"), this.$store.dispatch("getData", "popups")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                closePopup() {
                    this.show_popup = false;
                    document.getElementById('popup_backdrop').style.display = "none";
                }
            }
        })
    })
</script>