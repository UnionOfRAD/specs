
## Abstract

This document details common access patterns for Ajax requests, and examples of how each is accomplished using the framework. Please add any example access patterns not mentioned, as well as any best-practice examples of how to accomplish each one (there may be&mdash;and often is&mdash;more than one valid way). _Note that all examples assume jQuery._

## Rendering views or view sub-sections

In order to solve the problem of rendering pages differently in different contexts, without duplicating controller logic, it is beneficial to identify Ajax requests as a separate media type, such that they can be automatically rendered with different settings.

The following example is useful when the template content used in an Ajax request is significantly different from the content for a normal request:

**config/bootstrap/media.php**:
```php
use lithium\net\http\Media;

Media::type('ajax', ['application/xhtml+xml', 'text/html'], [
        'view' => 'lithium\template\View',
        'paths' => [
                'template' => [
                        '{:library}/views/{:controller}/{:template}.ajax.php',
                        '{:library}/views/{:controller}/{:template}.html.php'
                ],
                'layout' => '{:library}/views/layouts/default.ajax.php'
        ],
        'conditions' => ['ajax' => true]
]);
```

Setting `'conditions' => ['ajax' => true]` is the equivalent of calling `$this->request->is('ajax')` in the controller.

**views/hello_world/test.ajax.php**:
```
<?=$message; ?> an <strong>Ajax</strong> request.
```

**views/hello_world/test.html.php**:
```
<script type="text/javascript">
$(document).ready(function() {
        $('a[target]').bind('click', function(e) {
                $($(this).attr('target')).load(this.href);
                e.preventDefault();
        });
});
</script>

<p>
        <?=$message; ?> a regular request.
</p>
<?=$this->html->link('Load...', 'HelloWorld::test', ['target' => 'p:first']); ?>
```

**views/layouts/default.ajax.php**:
```
<?=$this->content(); ?>
```

Finally, don't forget to enable content negotiation for your controller(s):

```
        protected function _init() {
                $this->_render['negotiate'] = true;
                parent::_init();
        }
```

Viewing the page will produce a message from the standard template with a link that says `Load...`, and clicking this link will change the message in the `<p />` element, which will be rendered with the alternate Ajax template.

This setup also allows for no custom Ajax template, in which case rendering will fall back to the corresponding standard HTML template. This will render the page with no layout, allowing for an inline page refresh (assuming the default layout, this can be accomplished with `$("#content").load(this.href)`, or by setting the `target` attribute in the above example to `"#content"`).

## Sending & receiving JSON data

...

## Forms integration

...

