本项目是[commune/chatbot](https://github.com/thirdgerb/chatbot) 和 [commune/studio-hyperf](https://github.com/thirdgerb/studio-hyperf)的组件, 可将 [Rasa](https://rasa.io) 提供的 NLU 中间件引入项目.

```
composer require commune/compnt-rasa
```

## 注册 RasaComponent

CommuneChatbot 对 Rasa 服务做了基本的封装, 需要通过 ```Commune\Components\Rasa\RasaComponent``` 引入系统, 具体而言有三步:

1. 配置数组引入 ```Commune\Components\Rasa\RasaComponent```
1. ```NLUComponent::$nluSerivces``` 处, 注册```Commune\Components\Rasa\Services\RasaService```
1. 在配置数组 ```$chatbotConfig->host->sessionPipes``` 中引入中间件```Commune\Components\Rasa\RasaSessionPipe```

```php

// 机器人配置数组 ChatbotConfig

return [
    ...

    'components' => [
        ...

        Commune\Components\Rasa\RasaComponent::class => [
            // rasa http api 地址
            'server' => 'localhost:5005',

            // 如果通过 jwt 验证的话
            'jwt' => '',

            // 意图的置信度, 高于 0.7 才加入 highlyPossibleIntent
            'threshold' => 70,

            // 语料库同步的目标文件位置
            'output' => __DIR__ .'/resources/rasa.md',

            // rasa domain.md 的文件位置
            'domainOutput' => __DIR__ . '/resources/domain.yml',

            // 使用 Guzzle 调用 Rasa api 时的配置
            'clientConfig' => [
                'timeout' => 0.3,
            ],
        ],

        NLUComponent::class => [
            'nluServices' => [
                ...
                Commune\Components\Rasa\Services\RasaService::class,
            ]

        ],
    ],

    ...

    'host' => [
        ...

        'sessionPipes' => [
            ...
            Commune\Components\Rasa\RasaSessionPipe::class,
        ]
        ...
    ],

]
```

## 同步语料库

Rasa 所需要的语料库, 格式在 [Rasa training data format](https://rasa.com/zh-cn/rasa/nlu/training-data-format/) 可以查看.
CommuneChatbot 的本地语料库设计参考了 Rasa 定义的格式.

使用 ```Commune\Components\Rasa\Services\RasaService``` 可以直接将本地语料库同步到 rasa 的配置文件中.

具体内容请查看 [开发手册](https://github.com/thirdgerb/chatbot-book).

```
CommuneChatbot 开发了一批用多轮对话管理机器人自己的工具.

请选择您要使用的开发工具:  (1)

[1] nlu 语料库管理

> 1

选择功能 (1)

[1] 查看NLU命中的意图
[2] 同步语料库到NLU
[3] 同步语料库到本地存储
[4] 管理意图的语料样本

> 2

Commune\Components\Rasa\Services\RasaService : success
```
