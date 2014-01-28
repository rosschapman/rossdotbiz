---
layout: post
title:  "TILTW: Rails form tags, & jQuery prop()"
categories: tiltw ror jquery
---

Each week I learn something new.  At the end of the week, I pick a couple of things to write about that I think are cool. You may
<a title="Share on Twitter" data-network="twitter" data-action="share" href="https://twitter.com/share?url=http://internetross.me/tiltw-dec-30-jan-3&amp;text=@internetross, I like you, but..." target="_blank">
disagree</a>.

## Ruby on Rails Form Tags
I'm staring at this form button...then I click it and the text changes from "Save" to "Please wait..."  With some digging I discover that Rails offers an easy way to add scripted UI interactions to your forms with attributes in form tags.  Take this example:

{% highlight ruby %}
<%= form.submit 'Save', disable_with: 'Please wait...', disabled: true, class: 'button' %>
{% endhighlight %}

Some of these attributes, such as `disabled: true', hook to a small library of jQuery scripts, *jquery-ujs*, that handle various form UI as well as data transportation methods. Rails calls these scripts [“unobtrusive scripting adapters for jQuery”](http://github.com/rails/jquery-ujs/).  Some call it magic.

## jQuery's prop() & attr()
I honestly think I've never used jQuery's .prop() method before.  Not sure why, just haven't.  But, as you've noticed from above, this week I was messing with form buttons in a Rails [app](http://www.scienceexchange.com), and thinking about button states, and then went down a small rabbit hole investigating jQuery attribute methods and the DOM.

While I was impressed with Rails' opinionated (obscured, magic) tactic of form button state handling, I began thinking about alternative interaction paradigms for a button after click.  In that moment I had some daydream about a jQuery method for disabling attributes, like `.disabled()` or something.  So I googled for it, of course to be familiarly greeted in the top results by `.attr()` (duh), but also `.prop()`.

These two methods are really similar, but as I read the docs on both I realized I wasn't totally clear about the difference between a DOM property vs an attribute, and how to decide which of these methods to use for handling button states.

jQuery is partially to blame. Up until v1.6 was released in May of 2011 - basically around the time I started writing more  <a class="term-name" href="/glossary#production">production</a> jQuizzle - `.attr()` was the only method available to access properties or attributes.  v1.6 introduced `.prop()` because, as the jQuery docs maintain, "the .attr() method sometimes took property values into account when retrieving some attributes, which could cause inconsistent behavior."  `.prop()` explicitly separated retrieval of property values and attributes.

But what, then, really IS a property vs an attribute.  Well, here's a comment on StackOverflow about properties:

> "When a DOM node is created for a given HTML element, a corresponding property is created for each attribute in the HTML source code." [*](http://stackoverflow.com/questions/6003819/properties-and-attributes-in-html)

Ah, so a property is actually a representation of an attribute in the DOM tree.

So how do they differ?  StackOverflow again:

> "Where both a property and an attribute with the same name exists, usually updating one will update the other, but this is not the case for certain attributes of inputs, such as value and checked: for these attributes, the property always represents the current state while the attribute (except in old versions of IE) corresponds to the default value/checkedness of the input (reflected in the defaultValue / defaultChecked property)." [*](http://stackoverflow.com/questions/5874652/prop-vs-attr)

According to the above definition, `.prop()` is better suited for checking the value of attributes whose values we expect to change after DOM load.  For example, `value`, `checked`, and `disabled` will actually retain their serialized HTML attribute (value) after DOM load so if we were to alter these properties with javascript, retrieving their current value, after manipulation, would not be guaranteed by `attr()`.

Admittedly, this is all a little bit difficult to grasp at first considering the words themselves are so similar in meaning: "attribute", "property", and "value" are practically interchangeable in English vernacular. But it's so important - and more and more I find it myself to be true - that a jQuery dev understand how far the $ obfuscates a pure view of HTML and the DOM. It's totally possible to eff up your code if you were to mistakenly use `.attr()` at the wrong time! Sheesher.

Ok. First [TILTW](/glossary#tiltw) episode complete.  Happy new year and talk soon!

Also, I'm pretty new to technical writing. Would love to hear your <a title="Share on Twitter" data-network="twitter" data-action="share" href="https://twitter.com/share?url=http://internetross.me/tiltw-dec-30-jan-3&amp;text=@internetross, gotta tell you something bud..." target="_blank">
feedback</a>!