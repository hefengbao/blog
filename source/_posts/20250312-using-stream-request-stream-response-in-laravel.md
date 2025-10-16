---
title: Laravel 请求流式（Stream）接口返回流式Http 响应
date: 2025-03-12 17:17:17
tags:
  - stream-response
  - laravel
categories: Laravel
---

```php
<?php
use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Route;
use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\ResponseInterface;


Route::get('http', function (){
    return response()->eventStream(function (){
        $stream = Http::withHeaders([
            'Authorization' => 'Bearer '.env('DEEPSEEK_API_KEY'),
            'Accept' => 'application/json',
        ])
            ->throw()
            //->accept('application/x-ndjson')
            ->asJson()
            ->timeout(2000)
            ->withOptions(['stream' => true])
            ->post(env('DEEPSEEK_API_BASE_URL').'/chat/completions',[
                'messages' => [
                    [
                        'content' => 'Hello',
                        'role' => 'system',
                    ],
                    [
                        'content' => '西南财经大学',
                        'role' => 'user',
                    ]
                ],
                'model' => 'deepseek_r1.deepseek-r1', //deepseek_r1.deepseek-r1
                'frequency_penalty' => 0,
                'max_tokens' => 2048,
                'presence_penalty' => 0,
                'response_format' => [
                    'type' => 'text'
                ],
                'stream' => true
            ])
            ->toPsrResponse()
            ->getBody();

        $buffer = '';
        while (!$stream->eof()) {
            $char = $stream->read(1);

            $buffer .= $char;
            if ($char === "\n") {
                $line = trim($buffer);
                $buffer = '';

                if (!empty($line)) {
                    yield $line;
                }
            }
        }
    });
});


Route::get('guzzle', function (){
    $client = new \GuzzleHttp\Client([
        'base_uri' => config('swufe-chat.base_url')
    ]);

    return response()->eventStream(function () use ($client){
        $response = $client->request('POST', '/api/chat/completions', [
            'headers' => [
                'Authorization' => 'Bearer '.env('DEEPSEEK_API_KEY'),
                'Accept' => 'application/json',
            ],
            'json' => [
                'messages' => [
                    [
                        'content' => 'Hello',
                        'role' => 'system',
                    ],
                    [
                        'content' => '西南财经大学',
                        'role' => 'user',
                    ]
                ],
                'model' => 'deepseek_r1.deepseek-r1', //deepseek_r1.deepseek-r1
                'frequency_penalty' => 0,
                'max_tokens' => 2048,
                'presence_penalty' => 0,
                'response_format' => [
                    'type' => 'text'
                ],
                'stream' => true
            ],
            'stream' => true
        ]);

        $stream = $response->getBody();
        $buffer = '';
        while (!$stream->eof()) {
            $char = $stream->read(1);

            $buffer .= $char;
            if ($char === "\n") {
                $line = trim($buffer);
                $buffer = '';

                if (!empty($line)) {
                    yield $line;
                }
            }
        }
    });
});


// https://github.com/openai-php/client

Route::get('openai', function (){
    $client = OpenAI::factory()
        ->withApiKey(env('DEEPSEEK_API_KEY'))
        ->withBaseUri(env('DEEPSEEK_API_BASE_URL'))
        ->withHttpClient($httpClient = new \GuzzleHttp\Client())
        ->withStreamHandler(fn (RequestInterface $request): ResponseInterface => $httpClient->send($request, [
            'stream' => true // Allows to provide a custom stream handler for the http client.
        ]))
        ->make();

    return response()->eventStream(function () use ($client){
        $stream = $client->chat()->createStreamed([
            'model' => 'deepseek_r1.deepseek-r1',
            'messages' => [
                ['role' => 'user', 'content' => 'Hello!'],
            ],
            'stream_options'=>[
                'include_usage' => true,
            ]
        ]);

        foreach ($stream as $response) {
            yield $response->choices[0];
        }
    });
});
```

```php
$stream = Http::throw()
    ->accept('application/x-ndjson')
    ->asJson()
    ->withOptions(['stream' => true])
    ->post($endpoint, $requestData)
    ->toPsrResponse()
    ->getBody();

$buffer = '';

while ($stream->eof() === false) {
    $char = $stream->read(1);

    $buffer .= $char;
    if ($char === "\n") {
        $line = trim($buffer);
        $buffer = '';

        if (!empty($line)) {
            dump($line);
        }
    }
}
```

参考：

https://yellowduck.be/posts/using-streaming-http-responses-in-laravel


官方也有实现😓

https://laravel.com/docs/12.x/responses#streamed-json-responses