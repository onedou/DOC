#PHP7下使用MongoDB API

安装支持PHP7的扩展。

老版的MongoDB扩展是不支持PHP7的，需要下载重新编译支持PHP7的扩展。

手动编译PHP7的MongoDB扩展  
PHP手册关于老版扩展的文档  
PHP手册关于新版扩展的文档  

安装MongoDB的PHP类库。

mongo-php-library的github主页

使用composer安装。

    composer require "mongodb/mongodb=^1.0.0@beta"

操作完前两步就可以在PHP7里使用MongoDB了.

    <?php
    require_once __DIR__ . "/vendor/autoload.php";

    $manager = new MongoDB\Driver\Manager("mongodb://localhost:27017");
    $collection = new MongoDB\Collection($manager, "db.test");


    // 读取一条数据
    $data = $collection->findOne(array('id' => 1));

    // 读取多条数据
    $options = arrray(
        'projection' => array('id' => 1, 'age' => 1, 'name' => 1), // 指定返回哪些字段
        'sort' => array('id' => -1), // 指定排序字段
        'limit' => 10, // 指定返回的条数
        'skip' => 0, // 指定起始位置
    );

    $dataList = $collection->find(array('age' => 50), $options);

    // 插入一条数据
    $data = array('id' => 2, 'age' => 20, 'name' => '张三');