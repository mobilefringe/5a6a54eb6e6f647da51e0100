<template>
	<div v-if="dataloaded">
		<div class="page_header all_caps double_border_bottom">
			<div class="page_container text_left"> Stores Directory & Map </div>
		</div>
		<div class="page_container">
			<!-- for some reason if you do not put an outer container div this component template will not render -->
			<div class="store-list-left-block col-sm-4 padding_top_20">
		        <div id="mapsvg_store_detail_1" class="show_phone">
					<png-map :png-map-url="getPNGurl" v-bind:initial-position="'500 450'" v-if="mobile_store"></png-map>
				</div>
				<div class="store-list-container">
					<div class="dropdown_container hidden_phone">
						<p class="text_left">Select Category</p>
						<div class="category-DD-div">
							<v-select v-model="selectedCat" :options="dropDownCats" :searchable="false" :on-change="filterByCategory"></v-select>
						</div>
						<p class="text_left" style=" margin-top: 10px;">Sort Alphabetically</p>
						<div class="category-DD-div">
							<v-select v-model="selectedAlpha" :options="alphabet" :searchable="false" :on-change="filterStores"></v-select>
						</div>
					</div>
					<div class="show_phone" style="margin-top:20px">
					    <p class="text_left">Sort By Category :</p>
					    <div class="alphabet-dd  " >
    						<v-select v-model="selectedCat" :options="dropDownCats" :searchable="false" :on-change="filterByCategory" id="mobile_cat_list"></v-select>
    					</div>
					    <p class="text_left">Sort Alphabetically :</p>
    					<div class="alphabet-dd show_phone" >
						    <v-select v-model="selectedAlpha" :options="alphabet" :searchable="false" :on-change="filterStores" id="mobile_alpha_list"></v-select>
					    </div>
					</div>
					
					<ul class="store-listing text_left  padding_top_20">
						<li v-for="store in filteredStores" class="pointer">
							<p class="directory_store_name" v-on:click="addLandmark(store)">{{store.name}}</p>
						</li>
					</ul>
				</div>
			</div>
			<div class="col-sm-8">
				<div id="mapsvg_store_detail" class="padding_top_20">
					<png-map v-bind:png-map-url="getPNGurl" v-bind:initial-position="'500 450'" v-if="!mobile_store"></png-map>
				</div>
			</div>
		</div>
	</div>
</template>

<style>
    .v-select .open-indicator {
        right: 0 !important;
        top: 0 !important;
        bottom: initial !important;
        height: 100% !important;
        padding: 15px 25px 15px 20px !important;
        background-color: #9B9B9B !important;
        border-color: #fff !important;
        color: #fff !important;
    }

    .v-select .open-indicator:before {
        border-color: #fff !important;
        margin-top: -5px;
    }

    .v-select .dropdown-menu {
        font-family: arial;
        font-size: 14px;
        color: #000000;
        letter-spacing: 1.56px;
        display: block;
    }

    .v-select .dropdown-toggle {
        text-align: left;
        border-radius: initial;
    }
    .v-select .selected-tag{
        position:absolute;
    }
	#png_map{
	    width:1300px;
		height: 787px;
		min-width:1300px;
		min-height: 787px;
	}
	
	.text {
	    color:white;
	    font-size:16px;
	    padding-top:2px;
	}

</style>

<script>
var _extends=Object.assign||function(e){for(var t=1;t<arguments.length;t++){var r=arguments[t];for(var i in r)Object.prototype.hasOwnProperty.call(r,i)&&(e[i]=r[i])}return e};define(["Vue","vuex","vue-select","jquery","smooth-zoom","vue!png-map"],function(e,t){return e.component("stores-component",{template:template,data:function(){return{listMode:"alphabetical",selectedCat:"All",selectedAlpha:"All",alphabet:["All","A","B","C","D","E","F","G","H","I","J","K","L","M","N","O","P","Q","R","S","T","U","V","W","X","Y","Z"],filteredStores:null,dataloaded:!1,mobile_store:!1,windowWidth:0}},created:function(){var e=this;this.$store.dispatch("getData","categories").then(function(){e.dataloaded=!0,e.filteredStores=e.allStores},function(){console.error("Could not retrieve data from server. Please check internet connection and try again.")})},watch:{windowWidth:function(){this.mobile_store=this.windowWidth<=768?!0:!1}},mounted:function(){this.$nextTick(function(){window.addEventListener("resize",this.getWindowWidth),this.getWindowWidth()})},methods:{changeMode:function(e){this.listMode=e},updateSVGMap:function(e){this.map=e},addLandmark:function(e){this.svgMapRef.addMarker(e)},getWindowWidth:function(){this.windowWidth=window.innerWidth}},computed:_extends({},t.mapGetters(["property","timezone","processedStores","processedCategories","storesByAlphaIndex","storesByCategoryName","findCategoryById","findCategoryByName"]),{allStores:function(){return this.processedStores},allCatergories:function(){return this.processedCategories},dropDownCats:function(){var e=_.map(this.processedCategories,"name");return e.unshift("All"),e},getPNGurl:function(){return"https://www.mallmaverick.com"+this.property.map_url},svgMapRef:function(){return _.filter(this.$children,function(e){return"svg-map"==e.$el.className})[0]},filterStores:function(){if(letter=this.selectedAlpha,"All"==letter)this.filteredStores=this.allStores;else{var e=_.filter(this.allStores,function(e){return _.lowerCase(e.name)[0]==_.lowerCase(letter)});this.filteredStores=e}},filterByCategory:function(){if(category_id=this.selectedCat,category_id="All"==category_id||null==category_id||void 0==category_id?"All":this.findCategoryByName(category_id).id,this.breakIntoCol=!1,"All"==category_id)this.filteredStores=this.allStores;else{var e=(this.findCategoryById,_.filter(this.allStores,function(e){return _.indexOf(e.categories,_.toNumber(category_id))>-1}));this.filteredStores=e}}}),beforeDestroy:function(){window.removeEventListener("resize",this.getWindowWidth)}})});
</script>