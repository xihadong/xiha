####文章内容处理
1.提取内容url,替换拼接原有url标签格式
```php
foreach ($data as $key => $value) {

           preg_match_all('/<iframe[^>]*\s+src="([^"]*)"[^>]*><\/iframe>/', $value['content'], $iframematched); //提取iframe标签中的url

           foreach ($iframematched[1] as $k => $v){
               $url[$key][$k] = '@'. $v .'@'; //拼接
               $data[$key]['content'] = str_replace($iframematched[0][$k],$url[$key][$k],$value['content']);//iframe标签内容替换成拼接的字符串
           }
}
```

####分表
1.分表方式
```php

我事先建100个这样的表，message_00,message_01,message_02..........message_98,message_99.然后根据用户的ID来判断这个用户的聊天信息放到哪张表里面，你可以用hash的方式来获得，可以用求余的方式来获得，方法很多，各人想各人的吧。下面用hash的方法来获得表名：
查看复制打印?
 
    <?php 
    function get_hash_table($table,$userid) { 
     $str = crc32($userid); 
     if($str<0){ 
     $hash = "0".substr(abs($str), 0, 1); 
     }else{ 
     $hash = substr($str, 0, 2); 
     } 
       
     return $table."_".$hash; 
    } 
       
    echo get_hash_table('message','user18991');     //结果为message_10 
    echo get_hash_table('message','user34523');    //结果为message_13 
    ?> 
 
说明一下，上面的这个方法，告诉我们user18991这个用户的消息都记录在message_10这张表里，user34523这个用户的消息都记录在message_13这张表里，读取的时候，只要从各自的表中读取就行了。
 
优点：避免一张表出现几百万条数据，缩短了一条sql的执行时间
 
缺点：当一种规则确定时，打破这条规则会很麻烦，上面的例子中我用的hash算法是crc32，如果我现在不想用这个算法了，改用md5后，会使同一个用户的消息被存储到不同的表中，这样数据乱套了。扩展性很差。
```



