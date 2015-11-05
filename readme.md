# Yii2 RSS extension

[Yii2](http://www.yiiframework.com) RSS extension adds RSS-feed to your site

## Installation

### Composer

The preferred way to install this extension is through [Composer](http://getcomposer.org/).

Either run

```
php composer.phar require zelenin/yii2-rss "~0.1"
```

or add

```
"zelenin/yii2-rss": "~0.1"
```

to the require section of your ```composer.json```

## Usage

Add action to your controller:

```php
public function actionRss()
{
    $dataProvider = new ActiveDataProvider([
        'query' => Post::find()->with(['user']),
        'pagination' => [
            'pageSize' => 10
        ],
    ]);
    
    $response = Yii::$app->getResponse();
    $headers = $response->getHeaders();
    
    $headers->set('Content-Type', 'application/rss+xml; charset=utf-8');
    
    echo \Zelenin\yii\extensions\Rss\RssView::widget([
        'dataProvider' => $dataProvider,
        'channel' => [
            'title' => Yii::$app->name,
            'link' => Url::toRoute('/', true),
            'description' => 'Posts ',
            'language' => function ($widget, \Zelenin\Feed $feed) {
                return Yii::$app->language;
            },
            'image'=> function ($widget, \Zelenin\Feed $feed) {
                $feed->addChannelImage('http://example.com/channel.jpg', 'http://example.com', 88, 31, 'Image description');
            },
        ],
        'items' => [
            'title' => function ($model, $widget) {
                    return $model->name;
                },
            'description' => function ($model, $widget) {
                    return StringHelper::truncateWords($model->content, 50);
                },
            'link' => function ($model, $widget) {
                    return Url::toRoute(['post/view', 'id' => $model->id], true);
                },
            'author' => function ($model, $widget) {
                    return $model->user->email . ' (' . $model->user->username . ')';
                },
            'guid' => function ($model, $widget) {
                    $date = \DateTime::createFromFormat('Y-m-d H:i:s', $model->updated_at);
                    return Url::toRoute(['post/view', 'id' => $model->id], true) . ' ' . $date->format(DATE_RSS);
                },
            'pubDate' => function ($model, $widget) {
                    $date = \DateTime::createFromFormat('Y-m-d H:i:s', $model->updated_at);
                    return $date->format(DATE_RSS);
                }
        ]
    ]);
}
```

## Author

[Aleksandr Zelenin](https://github.com/zelenin/), e-mail: [aleksandr@zelenin.me](mailto:aleksandr@zelenin.me)
