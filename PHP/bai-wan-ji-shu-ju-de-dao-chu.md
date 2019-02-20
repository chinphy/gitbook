# 百万级数据的导出
示例：
```php
    public function output()
    {
        // 耗时较长，解除执行时间限制
        set_time_limit(0);
        // 内存限制，依情况设置
        ini_set('memory_limit', '128M');

        // 响应头
        $fileName = date('YmdHis', time());
        header('Content-Encoding: UTF-8');
        header("Content-type:application/vnd.ms-excel;charset=UTF-8");
        header('Content-Disposition: attachment;filename="' . $fileName . '.csv"');


        // 打开php标准输出流
        // 以写入追加的方式打开
        $fp = fopen('php://output', 'a');

        // 连接数据库
        $dbhost = '127.0.0.1';
        $dbuser = 'root';
        $dbpwd = 'root';
        $con = mysqli_connect($dbhost, $dbuser, $dbpwd);
        if (mysqli_connect_errno()) {
            die('connect error');
        }
        // 选择数据库
        $database = 'test';
        mysqli_select_db($con, $database);
        // 设置编码
        mysqli_query($con, "set names UTF8");


        // 用fputcsv从数据库中导出1百万的数据
        // 每次取1万条数据，分100步来执行，适当调整
        $step = 100;
        $nums = 10000;
        $where = "";

        // 设置标题
        $title = array('ID','姓名','年龄','性别');
        foreach($title as $key => $item) {
            $title[$key] =iconv("UTF-8", "GBK", $item);
        }
        // 将标题写到标准输出中
        fputcsv($fp, $title);

        for($s = 1; $s <= $step; $s++) {
            $start = ($s - 1) * $nums;
            $result = mysqli_query($con,
                                    "SELECT * FROM `test` ".$where." ORDER BY `id` LIMIT {$start},{$nums}");
            if($result) {
                while($row = mysqli_fetch_assoc($result)) {
                    foreach($row as $key => $item) {
                        //这里必须转码，不然会乱码
                        $row[$key] = iconv("UTF-8", "GBK", $item);
                    }
                    fputcsv($fp, $row);
                }
                mysqli_free_result($result); //释放结果集资源

                //每1万条数据就刷新缓冲区
                ob_flush();
                flush();
            }
        }
        //断开连接
        mysqli_close($con);
    }
```

>*还有一种做法就是数据分割成一个个小文件，然后打包成一个压缩文件下载*