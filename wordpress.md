# add a stylesheet

```
// load stylesheets
function load_stylesheets() {
	// register
	wp_register_style(
		'stylesheet', get_template_directory_uri() . '/style.css', '', 1, 'all'
	);

	// load style
	wp_enqueue_style('stylesheet');
}

add_action('wp_enqueue_scripts', 'load_stylesheets');
```

# add javascript file 

```
function load_js(){
	// name, path/file.js, dependencies, version, add_to_footer
	wp_register_script(
		'custom', get_template_directory_uri() . '/app.js', 'jquery', 1, true
	);

	wp_enqueue_script('custom');

}

add_action('wp_enqueue_scripts', 'load_js');
```

# Menu
- add support for menu in 'functions.php'
```
+ add_theme_support('menus');
```
