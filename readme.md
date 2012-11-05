# Useful Compass/Sass mixins

This is just my personal compilation of useful Compass mixins.

**Dependencies**: Sass >= 3.2, Compass >= 0.12.2

**Important Note**

I use mixins which use Compass' image-url() inside of mixins. There's a bug when using Sass 3.2.1 in combination with 0.12.2 which creates wrong image paths in compiled CSS ([See Compass issue 989](https://github.com/chriseppstein/compass/issues/989)).
To fix this just uninstall Sass 3.2.1:

```bash
sudo gem uninstall sass
```
and install the  version 3.2.0 alpha 275:

```bash
sudo gem install sass -v 3.2.0.alpha.275
```

## Contents

* [Clearfix](#clearfix)
* [Image Replacement](#image-replacement)
* [How to test mixins](#how-to-test-mixins)

## Clearfix

**File:** *_clearfix.scss*

You can choose between four different clearfix mixins:

1. **clearfix()** A.K.A The "Clearfix" hack without backslash hack ([css-tricks.com/snippets/css/clear-fix/](http://css-tricks.com/snippets/css/clear-fix/))
2. **clearfix-hack()** original A.K.A The "Clearfix" hack with IE for Mac support ([perishablepress.com/lessons-learned-concerning-the-clearfix-css-hack/](http://perishablepress.com/lessons-learned-concerning-the-clearfix-css-hack/))
3. **micro-clearfix()** by Nicolas Gallagher ([nicolasgallagher.com/micro-clearfix-hack/](http://nicolasgallagher.com/micro-clearfix-hack/))
4. **ym-clearfix()** YAML clearfix by Dirk Jesse ([github.com/djesse/yaml4-sass/blob/master/sass/yaml/core/base-modules/_float-handling.scss](https://github.com/djesse/yaml4-sass/blob/master/sass/yaml/core/base-modules/_float-handling.scss))

Find an overview on [css-tricks.com](http://css-tricks.com/snippets/css/clear-fix/).

### How to use

Just include the clearfix mixin:

```scss
.group {
	@include clearfix();
}
```

is compiled to:

```css
.group:after {
	visibility: hidden;
	display: block;
	font-size: 0;
	content: " ";
	clear: both;
	height: 0;
}
*:first-child+html .group 	{ zoom: 1; } /* IE7 */
* html .group				{ zoom: 1; } /* IE6 */
```

Or use the extends for multiple classes:

```scss
.group {
	@include clearfix();
}

.my-special-container {
	@extend .group;

	background-color: blue;
}
```

is compiled to:

```css
.group:after,
.my-special-container:after {
	visibility: hidden;
	display: block;
	font-size: 0;
	content: " ";
	clear: both;
	height: 0;
}
*:first-child+html .group,
*:first-child+html .my-special-container	{ zoom: 1; } /* IE7 */
* html .group,
* html .my-special-container				{ zoom: 1; } /* IE6 */

.my-special-container {
	background-color: blue;
}
```

## Image Replacement

**File:** *_image-replacement.scss*

This file includes three different image replacement mixins and two helper mixins for image dimensions.

1. **image-replace()** by Nicolas Gallagher (2011) ([nicolasgallagher.com/another-css-image-replacement-technique/](http://nicolasgallagher.com/another-css-image-replacement-technique/))
2. **image-replace-h5bp()** from HTML5 Boilerplate (2012) ([github.com/h5bp/html5-boilerplate/blob/master/css/main.css](https://github.com/h5bp/html5-boilerplate/blob/master/css/main.css))
3. **image-replace-indent()** The Phark Method (2003)<br>Very likely the most widely used method. Problematic in RTL situations (or anywhere text-align isn't left).

Find an overview on [css-tricks.com](http://css-tricks.com/css-image-replacement/).

### How to use

The text inside your container will be replaced by a css background image, but the text is still readable by screenreaders.

The HTML should look like:

```html
<h1 class="logo">
	Image Replacement Company Logo
</h1> 
```

```scss
.logo {
	@include image-replace();
}
```

is compiled to:

```css
.logo {
	font: 0/0 a;
	text-shadow: none;
	color: transparent;
	border: 0;
	background-color: transparent;
}
```

### Additional arguments (optional)

All three image replacement mixins have three optional arguments:

#### $image (the image file)

If you want to set the background to a single image, then just set the image filename as the first argument (default = null).

```scss
.logo {
	@include image-replace("logo.png");
}
```

is compiled to:

```css
.logo {
	font: 0/0 a;
	text-shadow: none;
	color: transparent;
	border: 0;
	background-color: transparent;
	background: transparent url('../../your/compass/image/directory/logo.png?1234567') no-repeat;
	width: 200px;
	height: 120px;
}
```

*There is no support for Compass sprites in here, yet. This you have to do manually:*

```scss
$sprites: sprite-map("icon/*.png", $spacing: 20px, $layout: vertical);

.sprites {
	background-image: sprite-url($sprites);
	background-repeat: no-repeat;
}

.logo {
	@extend .sprites;
	@include image-replace();

	background-position: sprite-position($sprites, logo, 0, 0);
	width: image-width(sprite-file($sprites, logo));
	height: image-height(sprite-file($sprites, logo));
}
```

#### $display (block, inline-block, table etc.)

With the second argument you can define the display mode. This might be useful if your element is an inline element like a span (default = null).

```scss
.logo {
	@include image-replace("logo.png", block);
}
```

is compiled to:

```css
.logo {
	font: 0/0 a;
	text-shadow: none;
	color: transparent;
	border: 0;
	background-color: transparent;
	dispplay: block;
	background: transparent url('../../your/compass/image/directory/logo.png?1234567') no-repeat;
	width: 200px;
	height: 120px;
}
```

You can also use only the second argument by setting the first to null:

```scss
.logo {
	@include image-replace(null, block);
}
```

#### $reset-border-background (true or false)

The third argument controls the reset options for borders and the background color (default = true);

```scss
.logo {
	@include image-replace("logo.png", null, false);
}
```

is compiled to:

```css
.logo {
	font: 0/0 a;
	text-shadow: none;
	color: transparent;
	background: transparent url('../../your/compass/image/directory/logo.png?1234567') no-repeat;
	width: 200px;
	height: 120px;
}
```

**To do:** If the $image argument is set, the background color reset isn't needed as compass sets the background color to transparent by itself.

## How to test mixins

If you want to test mixins you can use the test.scss. This file is compiled to css/scss/test.css and included in the index.html. Use Compass or Grunt to compile and watch your changes. Grunt will also lint the main.js JavaScript file.

**Compass:**

```bash
compass watch
```

### Grunt

For Grunt you'll need node.js. An installer you can find here: [nodejs.org](http://nodejs.org/)

Install Grunt globally:

```bash
sudo npm install -g grunt
``

Install Grunt modules locally (execude inside the directory "compass-sass"):

```bash
npm install
```

Then you could use the Grunt watch task:

```bash
grunt
```