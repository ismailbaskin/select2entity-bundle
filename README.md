select2entity-bundle
====================

##Introduction##

This is a Symfony2 bundle which enables the popular [Select2](https://select2.github.io) component to be used as a drop-in replacement for a standard entity field on a Symfony form.

The main feature that this bundle provides compared with the standard Symfony entity field (rendered with a html select) is that the list is retrieved via a remote ajax call. This means that the list can be of almost unlimited size. The only limitation is the performance of the database query or whatever that retrieves the data in the remote web service.

It works with both single and multiple selections. If the form is editing a Symfony entity then these modes correspond with many to one and many to many relationships. In multiple mode, most people find the Select2 user interface easier to use than a standard select tag with multiple=true with involves awkward use of the ctrl key etc.

The project was inspired by [lifo/typeahead-bundle](https://github.com/lifo101/typeahead-bundle) which uses the Typeahead component in Bootstrap 2 to provide similar functionality. Select2Entity can be used anywhere Select2 can be installed, including Bootstrap 3.

##Screenshots##

This is a form with a single selection field list expanded.

![Single select example](Resources/doc/img/single.png)

This is a form with a multiple selection field list expanded.

![Multiple select example](Resources/doc/img/multi.png)

##Installation##

Select2 must be installed and working first. I hope to setup a demo site but my setup is basically [BraincraftedBootstrapBundle](http://bootstrap.braincrafted.com) with Select2 installed for Bootstrap 3. Once the Braincrafted bundle is working, the only files I've needed to install are:

select2.js, select2.css, select2.png and select2-spinner.gif from https://github.com/select2/select2/tree/4.0.0

select2-bootstrap.css from https://github.com/t0m/select2-bootstrap-css/tree/bootstrap3. That gets it working for Bootstrap 3.

These files live in the Resources/public/js and Resources/public/css folders of one of my bundles and then included in my main layout.html.twig file.

* Add `tetranz/select2entity-bundle` to your projects `composer.json` "requires" section:

```javascript
{
    // ...
    "require": {
        // ...
        "tetranz/select2entity-bundle": "2.*"
    }
}
```
Note that this only works with Select2 version 4. If you are using Select2 version 3.X please use `"tetranz/select2entity-bundle": "1.*"` in `composer.json`

* Run `php composer.phar update tetranz/select2entity-bundle` in your project root.
* Update your project `app/AppKernel.php` file and add this bundle to the $bundles array:

```php
$bundles = array(
    // ...
    new Tetranz\Select2EntityBundle\TetranzSelect2EntityBundle(),
);
```

* Update your project `app/config.yml` file to provide global twig form templates:

```yaml
twig:
    form:
        resources:
            - 'TetranzSelect2EntityBundle:Form:fields.html.twig'
        
```
* Add this to the {% javascripts %} section of your layout file:

```
'@TetranzSelect2EntityBundle/Resources/public/js/select2entity.js'
```

##How to use##

Select2Entity is simple to use. In the buildForm method of a form type class, specify `tetranz_select2entity` as the type where you would otherwise use `entity`.

Here's an example:

```php
$builder
   ->add('country', 'tetranz_select2entity', [
            'multiple' => true,
            'remote_route' => 'tetranz_test_default_countryquery',
            'class' => '\Tetranz\TestBundle\Entity\Country',
            'text_property' => 'name',
            'minimum_input_length' => 2,
            'page_limit' => 10,
            'placeholder' => 'Select a country',
        ])
```

##Options##
* `class` is your entity class. Required
* `text_property` This is the entity property used to retrieve the text for existing data. 
If text_property is omitted then the entity is cast to a string. This requires it to have a __toString() method.
* `multiple` True for multiple select (many to many). False for single (many to one) select.
* `minimum_input_length` is the number of keys you need to hit before the search will happen.
* `page_limit` This is passed as a query parameter to the remote call. It is intended to be used to limit size of the list returned.
* `placeholder` Placeholder text.

The url of the remote query can be given by either of two ways: `remote_route` is the Symfony route. `remote_params` are can be optionally specified to provide parameters. Alternatively, `remote_path` can be used to specify the url directly.

###Defaults###
If not specified then these defaults will be used:
* `minimum_input_length` 2
* `page_limit` 10

These defaults can be changed in your app/config.yml file with the following format.

```yaml
tetranz_select2_entity:
    minimum_input_length: 2
    page_limit: 8
```

##AJAX Response##
The controller should return a `JSON` array in the following format. The properties must be `id` and `text`.

```javascript
[
  { id: 1, text: 'Displayed Text 1' },
  { id: 2, text: 'Displayed Text 2' }
]
```
