# PHP小聪明

# 如何优雅地把数组输出为可运行的php数组声明1
`print_r`输出的声明代码太老了，是php5.6以前的样子(array关键字)
那么如何把数组优雅地输出成可运行的数组声明代码呐，看下面

把数组转成json，然后把json中的`{` `}` `:` 分别替换成 `[` `]` `=>`
只要数组中没有`{}:`这样的字符，就能顺利替换成php数组的声明代码啦
```
str_replace(['{', '}', ':', '"'], ['[', ']', '=>', '\''], json_encode($data, JSON_PRETTY_PRINT))
```

最后吐个槽，php官方能不能把print_r加个参数，像json_decode一样支持最新的数组声明方式~

# 批量更新数据表内容
最近在用yii2做开发,所以执行sql的命令是yii2的，主要参考sql语句。下述代码的内容是删除废弃的时间戳字段，增加新的
```
<?php
//获取所有表
$tables = Yii::$app->db->createCommand("select table_name from information_schema.tables where table_schema='sample_platform_new' and table_type='base table'")->queryAll();
foreach ($tables as $table) {
    //获取表中的所有字段
    $columns = Yii::$app->db->createCommand("select COLUMN_NAME from information_schema.COLUMNS where table_name = '{$table['table_name']}'")->queryAll();
    foreach ($columns as $column) {
        if (in_array($column['COLUMN_NAME'], ['updatetime', 'createtime', 'update_time', 'create_time', 'created_at', 'updated_at'])) {
            //如果有updatetime/createtime update_time/create_time 删除
            Yii::$app->db->createCommand("alter table {$table['table_name']} drop column {$column['COLUMN_NAME']}")->execute();
        }
    }
    //增加created_at / updated_at字段
    Yii::$app->db->createCommand("alter table {$table['table_name']} add created_at int default 0")->execute();
    Yii::$app->db->createCommand("alter table {$table['table_name']} modify column created_at int comment '创建时间'")->execute();
    Yii::$app->db->createCommand("alter table {$table['table_name']} add updated_at int default 0")->execute();
    Yii::$app->db->createCommand("alter table {$table['table_name']} modify column updated_at int comment '更新时间'")->execute();
}
```
# 打印调用栈
```
<?php
$stack = debug_backtrace();
unset($stack[0]);
$html = '';
foreach($stack as $row) {
   $html .=$row['file'].':'.$row['line'].'行,调用方法:'.$row['function']."<p>";
}
echo $html;
```

# 按照自己想要的格式打印mysql表的列
```
SELECT GROUP_CONCAT(COLUMN_NAME SEPARATOR ",") FROM information_schema.COLUMNS 
WHERE TABLE_SCHEMA = 'db_name' AND TABLE_NAME = 'table_name'
```

# 显示所有错误信息
```
error_reporting(E_ALL);
ini_set('display_errors', 'On');
```

# apache和nginx 区别
* getallheaders只在apache下才支持
* nginx需要用此方式规避
```
if (!function_exists('getallheaders')) {
    function getallheaders() {
        $headers = array();
        foreach ($_SERVER as $name => $value) {
            if (substr($name, 0, 5) == 'HTTP_') {
                $headers[str_replace(' ', '-', ucwords(strtolower(str_replace('_', ' ', substr($name, 5)))))] = $value;
            }
        }
        return $headers;
    }
}
```
