```php
# phalcon 配置redis 作为session的存储器
$di->setShared(
    'session',
    function () {
            $session = new \Phalcon\Session\Adapter\Redis([
                'uniqueId'   => 'xxxxx',
                'prefix'     => '',
                'lifetime'   => 86400,
                'host'       => 'xxx.xx.xx.xxx',
                'port'       => 6379,
                'auth'       => 'xxxxxxxxx',
                'persistent' => false,
            ]);
        $session->start();
        return $session;
    }
);
```