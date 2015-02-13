# Wordpress [shortcodes]
See here for more information:
http://codex.wordpress.org/Shortcode_API

## Links

Wordpress Shortcode Generator
http://generatewp.com/shortcodes/

wp-includes/shortcodes.php
https://core.trac.wordpress.org/browser/tags/4.1/src/wp-includes/shortcodes.php#L0

Seven Shortcode Tips
http://www.wpbeginner.com/beginners-guide/7-essential-tips-for-using-shortcodes-in-wordpress/

## What is a shortcode?

“...a simple set of functions for creating macro codes for use in post content”
Put simply: when you’re filling out content for a post on your wordpress site, typing something like this:
```
	[example_shortcode]
```
would input code if example_shortcode happens to be a registered shortcode!

Super simple right?

### Why are they useful?
Firstly, your post will look much cleaner as:
```
	[example_shortcode]
```
than the alternative:
```
	<div class=’example-shortcode-class’>
		<p>
			My shortcode does a lot, and outputs a lot too!
			Check out this link that needs to appear on nearly every page:
			<a href=’http://www.google.ca’>
				A really useful site!
      </a>
  </p>
</div>
```
The more cumbersome and often code snippets like this are needed, the better it’ll look as a shortcode!

Another major reason is maintainability! Imagine that this link really was included in nearly every page in that exact format, and you needed to change it! Instead of sifting through all of your posts, if you use a shortcode you can simply change it at the source. Changing a shortcode will change how it is displayed in every post it is used in, making alterations easy-peasy!




Default Shortcodes

[audio]

[caption]

[embed]

[gallery]

[playlist]

[video]
[wp_caption]


How do shortcodes work?
Shortcode Parser
Well, if a shortcode is registered/defined in your project, the shortcode parser included in Wordpress will pick up on brackets:

	[]

and check for shortcodes. If the contents of these brackets do not match any registered shortcodes, the text will be printed completely as-is (brackets included).

Including Attributes
If a registered shortcode has attributes, you can pass values in the following format:

[example_shortcode attribute=”value”]
	OR
[example_shortcode attribute=’value’]
	OR
[example_shortcode attribute=value]
	OR
[example_shortcode “value”]
	OR
[example_shortcode value]
Surrounding Content
Shortcodes may also contain content if used like so:

	[example_shortcode] Your content goes here! [/example_shortcode]

The shortcode is opened with [example_shortcode] and closed by including a slash [/example_shortcode].

Escaping Shortcodes
If you wish to escape the use of a shortcode (that is, you need to display on your website the text and not the processed shortcode), simply surround the would-be shortcode in a single pair of brackets.

	[[example_shortcode]]
		OR
[[example_shortcode] Your content goes here! [/example_shortcode]]

How do I make my own?
Getting Started
You’ll need to create a PHP function located in the where? --> functions.php or custom plugin directory of your Wordpress site, and reference it using the built-in

add_shortcode()

function like so:

// [example_shortcode]
function example_shortcode_func( $atts ){
	return "This is an example shortcode!";
}
add_shortcode( 'example_shortcode', 'example_shortcode_func' );

This will output “This is an example shortcode!” whenever you use

	[example_shortcode]

in a Wordpress post on your site.

Adding Attributes
You can add attributes by accepting an array into your function:

// [example_shortcode attribute=”value”]
function example_shortcode_func( $atts ){
$a = shortcode_atts( array(
'attribute' => 'Now it even has attributes!'
), $atts );

	return "This is an example shortcode! {$a['attribute']}";
}
add_shortcode( 'example_shortcode', 'example_shortcode_func' );

You would be able to use the shortcode’s default attribute by not including a new one in use like so:

	[example_shortcode]

Which would display:

	This is an example shortcode! Now it even has attributes!

Or, you can input a new one by doing the following:

	[example_shortcode attribute=”This is my custom attribute sentence!”]

Which would display:

	This is an example shortcode! This is my custom attribute sentence!

Enclosing Shortcodes
Up until now, we’ve been playing with self-closing shortcodes that look like this:

	[example_shortcode]

Enclosing shortcodes appear like so:

	[example_shortcode] Your content goes here! [/example_shortcode]

and have one more piece to worry about. Your shortcode function will have to accept a

	$content

variable. Keep in mind that you can still have attributes in an enclosing shortcode. An enclosing shortcode function looks something like the following:

	// [example_shortcode attribute=”value”]
function example_shortcode_func( $atts, $content = null ){

	return ‘<b>’.$content.’</b>’;

}
add_shortcode( 'example_shortcode', 'example_shortcode_func' );

Note that

	$content

is seen in two places, wherein it is accepted and also used in output. This:

	[example_shortcode] This is an example of an enclosing shortcode! [/example_shortcode]

Would display as such:

	RAW:
	<b>This is an example of an enclosing shortcode!</b>
	ACTUAL:
	This is an example of an enclosing shortcode!

Parsing/Rendering
Please note that shortcodes are parsed after built-in formatting has run its course, so if you’d like the auto-formatting to take place on any HTML included in your shortcode function you’ll have to use

wpautop()
OR
wptexturize()

If a plethora of HTML is going to be outputted by your shortcode, try including

	ob_start();
	AND
	return ob_get_clean();

 in addition to closing and reopening your PHP as follows:

	// [example_shortcode]
function example_shortcode_func( $atts ){
	
ob_start();
	?>
	<b>You can input HTML here!</b>
	<?PHP

	return ob_get_clean();;
}
add_shortcode( 'example_shortcode', 'example_shortcode_func' );

The

	ob_start();

is used to convert that HTML area into a string that can be outputted correctly.




What are some related and useful functions?
add_shortcode( $tag, $function )
This is used to register a new shortcode, where

	$tag

is the name of the new shortcode, and

	$function

is the function that you want to execute whenever the shortcode is called.

remove_shortcode( $tag )
This is used to deregister an existing shortcode, where

	$tag

is the name of the shortcode in question.

remove_all_shortcodes()
This deregisters all existing shortcodes.

shortcode_atts( $pairs, $atts )
This is used for setting attributes in a shortcode function, where

	$pairs


is an array of set defaults (attribute names and attribute values) and

	$atts

is an array of the attributes and values being passed in during use of the shortcode.

do_shortcode( $content )
This is used to execute registered shortcodes as detected in

	$content

If no registered shortcodes are detected,

	$content

will be displayed as-is.





What are some things to watch out for?
Nesting Shortcodes
Should only be done by using

	do_shortcode()

to include a shortcode within a shortcode function. Even so, you cannot nest a shortcode of the same name within itself.

Mixing Enclosed and Closed Versions of the Same Shortcode
May yield unexpected results.

Using Hyphens in a Shortcode Name
It is recommended you avoid hyphens.

	[example_shortcode]

is  more stable than

	[example-shortcode]

because the parser reads the shortcode name up until the point in which the hyphen is located. This means, in our example the ‘-shortcode’ part of the name ‘example-shortcode’ would be excluded in execution. In other words, the following would be treated as the same shortcode (whichever shortcode beginning with ‘example’ was registered first):

	[example-shortcode1]
[example-shortcode2]

Using Square Brackets in an Attribute
Is not supported.

Using HTML in an Attribute
Wordpress 4.0 and beyond supports validated HTML in attributes; Before this, HTML specific characters (<, >) were not supported in attributes.

Using Square Brackets Around a Shortcode
May yield unexpected results.
