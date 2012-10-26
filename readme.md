# Content - Laravel bundle

*Content* is a bundle for the [Laravel PHP Framework](https://github.com/laravel/laravel) that enables
you to quickly add Markdown based content pages to your site. It is inspired by the
[Stacey CMS](http://staceyapp.com).

Features:

- Easy to add new pages. Content is based on Markdown.
- All content is text-based and therefore easy to version using GIT.
- No database needed.
- Customizable with user-defined properties.
- Filter functions.

## Installation

Use Artisan to install *Content*:

```bash
php artisan bundle:install content
```

Enable it by adding the following to your `bundles.php` file:

```php
return array(
  'content' => array('auto' => true),
)
```

And finally, create a directory called 'content' under the storage directory. This is where the
content pages will reside.

## Usage

### Define the content

First we need to create a content definition file. This file describes all the pages that will be
handled by the *Content* bundle.

The content definition file should be created at *storage/content/content.json* and looks like this:

```javascript
{
	"pages": [
		{
			"path": "projects",
		},
		{
			"path": "projects/moon-tower",
		},
		{
			"path": "projects/time-machine",
		},
		{
			"path": "about"
		}
	]
}
```

The pages array contains page objects that each contain a path.
This path references the URI to handle and also a directory under 
the *storage/content* directory that contains the actual content page. This content page
has the name *page.md* and is in the Markdown format. 

Given the above content definition and the domain www.mydomain.com, this means that the following
URIs will be handled:

www.mydomain.com/projects (content file: storage/content/projects/page.md)
www.mydomain.com/projects/moon-tower (content file: storage/content/projects/moon-tower/page.md)
www.mydomain.com/projects/time-machine (content file: storage/content/projects/time-machine/page.md)
www.mydomain.com/about (content file: storage/content/projects/page.md)

To have Laravel catch all remaining routes and show a page based on the content definitions you should 
add this route below your other routes in *application/routes.php*:

```php
Route::get('(.*)', function () {
	return Content::makeView(URI::current());
});
```

### Filling a template

By default the Content::makeView call will render the view named *page.blade.php*. You can then 
fill the view using the *$page* object.

**$page->getTitle()** - The title of the page.  
**$page->getContent()** - The Markdown formatted content of the content file.

### Overriding behaviour

**Overriding the view**

The view used to render the page can be overridden by defining a property called *template*:

```javascript
{
	"path": "projects",
	"template": "mypage"
}
```

**Overriding the title**

The title of the page is derived from the slug of the path. It is created by removing 
the hypens, making all words lowercase and then make the first letter of each word uppercase.
This behaviour can be overridden by defining a property called *title*:

```javascript
{
	"path": "projects/iphone-game",
	"title": "iPhone game"
}
```

### Additional properties

By specifying additional properties in the content definition file, you can pass any value
to the view. 

Define the properties:

```javascript
{
	"path": "projects/time-machine",
	"description": "a short meta description",
	"comments": true,
	"disqusid": "XXX-YYY-ZZZ",
	"script-id": "time-machine"
}
```

Get the properties with **$page->getValue('key')**. If a property does not exist for a page
*getValue()* will return null so that your template can easily handle non-defined properties.

### Filtering



### Handling the index page

Take these steps if you want to have the Content bundle handle the root page ('/') of your site.

Add a page with a path named *index*.

```javascript
{
	"path": "index"
}
```

Change the content of the catch-all route to:

```php
$uri = URI::current();
if ($uri == '/') {
 	$uri = 'index';
}
return Content::makeView($uri);
```

And create your root content file at *storage/content/index/page.md*.

## Roadmap

In the future I would like to add functions for iterating over the parents and 
children of a page and add functions for sorting.  
Pull requests are welcome!

## License

Licensed under the MIT License.
