<?php
require __DIR__ . '/vendor/autoload.php';

use Psr\Http\Message\ResponseInterface as Response;
use Psr\Http\Message\ServerRequestInterface as Request;
use Slim\Factory\AppFactory;

$app = AppFactory::create();

// Root check
$app->get('/', function (Request $request, Response $response) {
    $response->getBody()->write("🎬 Video API is running on Render!");
    return $response;
});

// Video Generate API
$app->post('/generate-video', function (Request $request, Response $response) {
    $data = $request->getParsedBody();
    $prompt = $data['prompt'] ?? 'Default video prompt';

    // বাইরের ভিডিও জেনারেশন সার্ভিস কল
    $client = new \GuzzleHttp\Client();
    $apiResponse = $client->post('https://example-video-api.com/generate', [
        'json' => [
            'prompt' => $prompt,
            'duration' => 10,
            'format' => 'mp4'
        ]
    ]);

    $body = $apiResponse->getBody()->getContents();

    $response->getBody()->write(json_encode([
        'status' => 'success',
        'message' => 'Video generation started!',
        'api_response' => json_decode($body, true)
    ]));

    return $response->withHeader('Content-Type', 'application/json');
});

$app->run();
