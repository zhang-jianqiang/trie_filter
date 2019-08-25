[toc]
# PHP敏感词过滤扩展
## 安装
### 安装libdatrie
```
$ curl -O ftp://linux.thai.net/pub/ThaiLinux/software/libthai/libdatrie-0.2.4.tar.gz
$ tar zxvf libdatrie-0.2.4.tar.gz
$ cd libdatrie-0.2.4
$ make clean
$ ./configure --prefix=/usr/local/libdatrie
$ make
$ make install
```
### 安装PHP trie_filter扩展
```
$ git clone https://github.com/zhang-jianqiang/trie_filter
$ cd trie_filter
$ phpize
// /usr/bin/php-config要修改成自己的php-config的目录
$ ./configure --with-php-config=/usr/bin/php-config --with-trie_filter=/usr/local/libdatrie
$ make
$ make install
```
### 修改php配置文件php.ini
修改`php.ini`文件添加这一行`extension=trie_filter.so`，并重启php

## 使用
```php
<?php
    // 生成词典文件
    $arrWord = file('./word.txt');
    foreach ($arrWord as $k => $v) {
	    trie_filter_store($resTrie, $v);
    }
    trie_filter_save($resTrie, __DIR__ . '/blackword.tree');

    // 使用词典进行过滤
    $resTrie = trie_filter_load(__DIR__ . '/blackword.tree');
    $strContent = 'hello word2 word1';
    $arrRet = trie_filter_search($resTrie, $strContent);
    print_r($arrRet); //Array(0 => 6, 1 => 5)
    echo substr($strContent, $arrRet[0], $arrRet[1]); //word2
    $arrRet = trie_filter_search_all($resTrie, $strContent);
    print_r($arrRet); //Array(0 => Array(0 => 6, 1 => 5), 1 => Array(0 => 12, 1 => 5))
    trie_filter_free($resTrie);
    // 说明：这里返回的是脏字的开始位置和结束位置，是以字节为单位的，如果是汉字的话一个utf8汉字则占三个字节
?>
```
