# 百万级数据的导出
使用php:///output，打开一个流去输出数据，边从数据库查找边向浏览器输出，优点是效率极高，不会占用太多服务器端资源，缺点是不能断点续传
```php
    function output()
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

还有一种做法就是数据分割成一个个小文件，然后打包成一个压缩文件下载
```php
    function output()
    {
        set_time_limit(0);
        ini_set('memory_limit', '128M');

        // 标题
        $title = array('ID','姓名','年龄','性别');
        foreach($title as $key => $item) {
            $title[$key] =iconv("UTF-8", "GBK", $item);
        }

        // 连接数据库
        $dbhost = '127.0.0.1';
        $dbuser = 'root';
        $dbpwd = 'root';
        $con = mysqli_connect($dbhost, $dbuser, $dbpwd);
        if (mysqli_connect_errno()) {
            die('connect error');
        }
        $database = 'test'; // 选择数据库
        mysqli_select_db($con, $database);
        mysqli_query($con, "set names UTF8"); // 设置编码

        $step = 100;
        $nums = 10000;
        $where = '';
        // 文件名存取器
        $fileNameArr = [];

        for($s = 1; $s <= $step; $s++) {
            $fp = fopen( $s . '.csv', 'w'); //生成临时文件
            $fileNameArr[] =  $s . '.csv';

            // 将数据通过fputcsv写到文件句柄
            fputcsv($fp, $title);

            $start = ($s - 1) * $nums;
            $result = mysqli_query($con,
                                    "SELECT * FROM `test` ".$where." ORDER BY `id` LIMIT {$start},{$nums}");
            if($result) {
                while($row = mysqli_fetch_assoc($result)) {
                    foreach($row as $key => $item) {
                        // 这里必须转码，不然会乱码
                        $row[$key] = iconv("UTF-8", "GBK", $item);
                    }
                    fputcsv($fp, $row);
                }

                mysqli_free_result($result); // 释放结果集资源

                // 刷新缓冲区
                ob_flush();
                flush();

                //每生成一个文件关闭
                fclose($fp);
            }
        }

        //进行多个文件压缩
        $zip = new \ZipArchive();
        $filename = "info.zip";
        $zip->open($filename, \ZipArchive::CREATE); // 打开压缩包
        foreach ($fileNameArr as $file) {
            $zip->addFile($file, basename($file)); // 向压缩包中添加文件
        }
        $zip->close(); // 关闭压缩包
        
        // 删除csv临时文件
        foreach ($fileNameArr as $file) {
            unlink($file); 
        }

        //输出压缩文件提供下载
        header("Cache-Control: max-age=0");
        header("Content-Description: File Transfer");
        header('Content-disposition: attachment; filename=' . basename($filename)); // 文件名
        header("Content-Type: application/zip"); // zip格式的
        header("Content-Transfer-Encoding: binary"); //
        header('Content-Length: ' . filesize($filename)); //
        @readfile($filename);// 输出文件;
        unlink($filename); // 删除压缩包临时文件
    }
```