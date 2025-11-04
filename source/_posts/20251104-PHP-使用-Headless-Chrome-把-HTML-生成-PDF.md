---
title: PHP 使用 Headless Chrome 把 HTML 生成 PDF
date: 2025-11-04 10:50:27
tags:
  - laravel
  - pdf
  - headless-chrome
categories: PHP
permalink: 20251104-convert-html-to-pdf-using-headless-chrome-in-php
---
可以使用 [wkhtmltopdf](https://wkhtmltopdf.org/)

这里使用 [chrome-php/chrome: Instrument headless chrome/chromium instances from PHP](https://github.com/chrome-php/chrome) 来实现。

```shell
composer require chrome-php/chrome
```

```php
namespace App\Services;

use HeadlessChromium\BrowserFactory;

class HtmlToPdfConverter
{
    /**
     * Render a PDF from the given HTML.
     */
    public static function render(string $html): string
    {
        $browser = (new BrowserFactory())->createBrowser([
            'windowSize' => [1920, 1080]
        ]);

        $page = $browser->createPage();

        $page->setHtml($html);

        return base64_decode(
            $page->pdf([
                'printBackground' => true
            ])->getBase64()
        );
    }
}
```

```php
use App\Services\HtmlToPdfConverter;

Route::get('/html-to-pdf', function () {
    $pdf = HtmlToPdfConverter::render('<h1>Hello World</h1>');

    return response($pdf, 200, [
        'Content-Type' => 'application/pdf',
        'Content-Disposition' => 'inline; filename="hello.pdf"'
    ]);
});
```


参考：

[Convert HTML to PDF using Headless Chrome in PHP — Amit Merchant — A blog on PHP, JavaScript, and more](https://www.amitmerchant.com/convert-html-to-pdf-using-headless-chrome-in-php/)