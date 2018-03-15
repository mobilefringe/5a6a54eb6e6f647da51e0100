<template>
    <div>
        <a href="https://www.instagram.com/shopbdc_" target="_blank"><i class="fa fa-instagram" aria-hidden="true"></i></a>
        <a href="https://www.facebook.com/bonniedoonsc" target="_blank"><i class="fa fa-facebook-official" aria-hidden="true"></i></a>
        <!--<a href="https://www.pinterest.ca/bonniedoonsc" target="_blank"><i class="fa fa-pinterest" aria-hidden="true"></i></a>-->
        <a href="//codecloud.cdn.speedyrails.net/sites/5a8c43eb6e6f641a29020000/image/jpeg/1521054167714/28907698_10156615165667323_2001433750_n.jpg" data-lightbox="Snapchat" data-title="BCC Snapchat"> <i class="fa fa-snapchat-ghost"></i></a>
        <a href="https://www.twitter.com/bonniedoonsc" target="_blank"><i class="fa fa-twitter" aria-hidden="true"></i></a>
    </div>
    
</template>
<script>
    define(["Vue", "vuex", "lightbox"], function(Vue, Vuex, Lightbox) {
        Vue.use(Lightbox);
        return Vue.component("social-links", {
            template: template, // the variable template will be injected,
        });
    });
</script>