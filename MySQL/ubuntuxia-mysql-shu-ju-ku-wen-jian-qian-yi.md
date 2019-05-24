准备：

   1. 保证新datadir的所有上级目录拥有'r'和'w'权限
   2. 确定当前datadir的位置,在mysql中执行:
   
   ```sql
      show variables like '%dir%';
   ```
   3. 停止MySQL服务

步骤：

    
  1. 拷贝或者移动原datadir到新datadir
  
  ```bash
     # 我使用拷贝,风险更小. -a 可以保留原文件的属性
     sudo cp -a /var/lib/mysql /mnt/data/mysql
     # 或者 
     sudo mv /var/lib/mysql /mnt/data/mysql
  ``` 
  
  2. 修改配置文件,有两个地方要改
     
     a. /etc/mysql/my.cnf 
        找到datadir 改为新的datadir
     
     b. /etc/apparmor.d/usr.sbin.mysqld
         
     ```bash
      # 找到
      /var/lib/mysql/ r,
      /var/lib/mysql/** rwk,
      # 改成新的data_dir

     ```
 
  3. 重启服务
    ```bash
      sudo service apparmor restart
      sudo service mysql start
    ```
      
  