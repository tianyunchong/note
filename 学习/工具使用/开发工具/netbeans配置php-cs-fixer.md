![](images/2021-05-21-18-21-57.png)
```php
<?php
$finder = PhpCsFixer\Finder::create()
    ->exclude('somedir')
    ->in(__DIR__);

return PhpCsFixer\Config::create()
    ->setRules([
        '@PSR2' => true,
        "array_indentation" => true,
        "binary_operator_spaces" => [
            "default" => "align_single_space_minimal"
        ]
    ])
    ->setFinder($finder)
;
```