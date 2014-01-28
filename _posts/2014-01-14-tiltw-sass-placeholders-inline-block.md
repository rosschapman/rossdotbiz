---
layout: post
title:  "TILTW: Sass Placeholders & inline-block"
categories: tiltw ror jquery
---

Each week I learn something new.  At the end of the week, I pick a couple of things to write about that I think are cool. You may
<a title="Share on Twitter" data-network="twitter" data-action="share" href="https://twitter.com/share?url=http://internetross.me/tiltw-dec-30-jan-3&amp;text=@internetross, I like you, but..." target="_blank">
disagree</a>.

## Wait, Sass has Placeholders?!  Wait, what are Placeholders?!
Up until this week I was using Sass mixins anywhere I needed to recycle a special block of rule declarations and values, whether they received arguments or not.  Apparently, as I know now, this is about a 1.5 years outdated approach. (awkward laughter)

Generally, mixins are *extremely* powerful to help DRY your Sass input - primarily because you can pass arguments. Here's an example of a mixin we use at at [Science Exchange](http://scienceexchange.com) to help with cross-browser transparent backgrounds:

{% highlight css %}
=transparent($color, $alpha)
  $rgba: rgba($color, $alpha)
  $ie-hex-str: ie-hex-str($rgba)
  background-color: transparent
  background-color: $rgba
  filter: progid:DXImageTransform.Microsoft.gradient(startColorstr=#{$ie-hex-str},endColorstr=#{$ie-hex-str})
  zoom: 1
{% endhighlight %}

The annoyance with mixins is that the CSS output can become a bit over-loquacious. Now, most of the time this extra code won't matter for load times - especially if our designs are lean to begin with.  But in the same way we should understand how jQuery obfuscates and translates plain javascript, we should be concerned with how preprocessors like Sass do the same for CSS.  Take the following example:

```css
@mixin media
  overflow: hidden
  &:first-child
      float: left
  &:last-child
      overflow: hidden
.status
    @include media

.profile
    @include media
```

The resultant CSS output will be:

```css
.status {
  overflow: hidden;
}
.status:first-child {
  float: left;
}
.status:last-child {
  overflow: hidden;
}

.profile {
  overflow: hidden;
}
.profile:first-child {
  float: left;
}
.profile:last-child {
  overflow: hidden;
}
```

Ok, let's take a look at how the placeholder selector would handle the same situation:

```css
%media
    overflow: hidden
    &:first-child
        float: left
    &:last-child
        overflow: hidden
.status
    @extend %media

.profile
    @extend %media
```

Looks like this compiled:

```css
.status, .profile {
  overflow: hidden;
}
.status:first-child, .profile:first-child {
  float: left;
}
.status:last-child, .profile:last-child {
  overflow: hidden;
}
```

Now that's DRY. Arid. Like the ashes from whence Daenerys Targaryen emerged baptized by fire and reborn a queen of dragons.

What is Sass is doing differently with placeholders is moving the selectors to the properties instead of copying the properties into the selectors, working "with the cascade" vs "working around the cascade" [](http://chriseppstein.github.io/blog/2012/08/23/sass-3-2-is-released/).  That is awesome!

If none of this just made sense, try and think of placeholders as mixins without arguments that are discarded from the output unless actually used.  There are broader implications for using placeholders effectively.  I liked Ian Taylor Storm's [walk-through](http://ianstormtaylor.com/oocss-plus-sass-is-the-best-way-to-css/), which might give you some more ideas.

## Inline-block actin like words, word?
Using `display: inline-block` can save you a lot troubles floating elements - which defy [normal flow](http://www.w3.org/TR/CSS21/visuren.html#normal-flow) - and I find myself relying on it more and more to save on CSS weight for tiered navigations, grid-like parts, faux-tables, etc...  Remember when IE6 and sometimes IE7 didn't support inline-block?  That was...[annoying](/glossary#annoying).

If you've used inline-block before you know that browsers will generate an extra space between each element.  I half-assumed that inline-block elements were similarly treated to inline elements.  Which was correct.  Just like inline elements, inline-block elements are treated like inline *content" - i.e. words - and receive generic behavior - a ~4px space between elements - and properties like word-spacing and font-size.  Here's a simple codepen to demonstrate such and how to use negative word-spacing to fix the generic space between inline elements.

<p data-height="268" data-theme-id="0" data-slug-hash="KCDiz" data-default-tab="result" class='codepen'>See the Pen <a href='http://codepen.io/rosschapman/pen/KCDiz'>Spaces between inline elements</a> by Ross (<a href='http://codepen.io/rosschapman'>@rosschapman</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//codepen.io/assets/embed/ei.js"></script>

It feels counterintuitive to use word-spacing to handle layout issues for block elements, but it may be one of the less [weirder](http://css-tricks.com/fighting-the-space-between-inline-block-elements/) ways.  Generally it seems counterintuitive to me for any kind of element inheriting block box properties to also inherit CSS properties that are "textual" - i.e. word-spacing, font-size, letter-spacing.  But I supposed you can't fault the tendency to introduce semantically textual properties to the spec when, at the start, most inline content on the web was just text.

For more on the cryptic visual formatting model and how user agents (browsers) process the document tree, see the impossible-to-comprehend-except-after-reading-100x World Wide Web Consortium [spec](http://www.w3.org/TR/CSS2/visuren.html).