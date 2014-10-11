** Rails CSS **

This document describes how I plan to manage css on new rails projects.

*** Directory structure ***


    /app 
      /assets
        /stylesheets
	  application.scss
	  module1.scss
	  module2.scss
          /includes
	    application_inc.scss
	    module1_inc.scss
	    module2_inc.scss
	  /embeded
	    application_embed.scss
	    module1_embed.scss
	    module2_embed.scss


* /stylesheets - contails stylesheets to be included via the asset pipeline
* /styleshees/embeded - contains (minimal) style definitions to embed diectly within a page (to allow correct rendering before the appliction stylesheet has been downloaded).
* /stylesheets/includes - contains all the scss mixins and variable definitions

*** Including stylesheets in layouts ***

include stylesheets via javascript before the losing body tag 
(https://developers.google.com/speed/docs/insights/OptimizeCSSDelivery)

    <script>
      var cb = function() {
        var l = document.createElement('link'); l.rel = 'stylesheet';
        l.href = 'small.css';
        var h = document.getElementsByTagName('head')[0]; h.parentNode.insertBefore(l, h);
      };
      var raf = requestAnimationFrame || mozRequestAnimationFrame ||
          webkitRequestAnimationFrame || msRequestAnimationFrame;
      if (raf) raf(cb);
      else window.addEventListener('load', cb);
    </script>

A helper function to aid this is recomended!


include critial styles ( those needed to prevent the "Flash of Unstyled Content" ) in the head

A helper along these lines can do the trick 


    def embed_scss(asset_name)
      asset = Rails.application.assets.find_asset(asset_name)
      Sass::Engine.new(
        asset,
        style: :compressed,
        syntax: :scss,
        line_comments: false
      ).render
    end



