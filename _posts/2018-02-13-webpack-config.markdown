---
layout: post
title:  "Understanding Webpack Basics"
date:   2018-02-13 23:56:45
description: A general overview and tutorial for Webpack configuration in Vue
categories:
permalink: webpack-basics
---

Webpack is a module bundling library common in modern Javascript projects which has a reputation for being esoteric and difficult to configure. This causes unfamiliar users to rely on using template configurations - which are great when you want to get coding fast - but make your build process a black box. Eventually, you'll want to configure or adjust your build pipeline (adding a linter, using SCSS, developing server side rendering, whatever) and you'll need to understand what's going on underneath the hood.

Hence my reasoning for this post! I wanted to start exploring server side rendering for Vue, but found the available templates too unwieldy. I'm aiming in this post to provide a gentle introduction to basic Webpack configuration for Vue front ends. While server side rendering is beyond the scope of this post, I will explain a configuration which gives us access to Modules (import), Babel, and Vue..

Webpack has 3 basic foundations for it's configuration: an input file, an output file, and the functions to apply to encountered filetypes.


{% highlight javascript %}

// webpack.config.js

module.exports = {

  // input file
  entry: '',

  // output file
  output: '',

  // the functions to apply
  module: {
    rules: [
      {

      }
    ]
  }

}

{% endhighlight %}

## Input

Webpack works by building a dependency graph. This means it builds a tree of required files, and for this to occur, it needs to know where to start. This system is what allows import statements to work in front end applications.

{% highlight javascript %}

// src/main.js

import Vue from 'vue'
import App from './app'

new Vue({
  el: '#app',
  template: '<App/>',
  components: { App }
})

{% endhighlight %}

## Output

Webpack outputs (bundles) its results to a single file, which is usually served by a web site to provide the necessary files needed for a front end to work. Bundling is ideal as the client application downloads everything it needs in one round trip - the alternative being multiple script tags, which are slower to load and more error prone (as order is important).

{% highlight html %}

// index.html

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Vue Example</title>
  </head>
  <body>
    <div id="app"></div>
    <script src="dist/build.js"></script>
  </body>
</html>

{% endhighlight %}

## Rules

Webpack needs to know how it ought to transform each type of file it encounters. Given its ubiquity for build pipelines, loaders are available for common libraries and tooling. In the example code I'm providing, there are two rules applied: one on javascript files, to transform ES6 to ES5 for browser support, and the other on .vue files, to allow for single file components.

{% highlight html %}

// this would cause an error on a browser without Webpack's vue-loader

<template>
  <div class="message">
    {% raw %}{{ message }}{% endraw %}
  </div>
</template>

<script>
export default {

  data: function() {
    return {
      message: 'hello, world'
    }
  }

}
</script>

<style scoped>
.message {
  color: red;
}
</style>

{% endhighlight %}

Rules work using a simple syntax: an expression match for the filetype, and the loader to apply on that filetype. Remember to install these packages with npm or yarn!

{% highlight javascript %}

  module: {

    rules: [

        {
          // Does the file end in .js?
          test: /\.js$/,
          // Apply babel to it
          loader: 'babel-loader',
          // Exclude node_modules (3rd party)
          exclude: /node_modules/
        },
        {
          // Does the file end in .vue?
          test: /\.vue$/,
          // Apply vue-loader to it
          loader: 'vue-loader'
        }

    ]

  }

{% endhighlight %}

The full code for this configuration can be found on [github](https://github.com/laaksomavrick/webpack-config). Clone the project, run `npm install`, and then serve the project with `webpack-dev-server` from the base directory. This should provide you a good starting point for any single page application.
