准备：
 
 
   1. 先停止MySQL服务
   2. 保证新data_dir的所有上级目录拥有'r'和'w'权限
   3. 确定当前data_dir的位置,在mysql中执行:
   
   ```sql
      show variables like '%dir%';
   ```
步骤：

    
  1. 拷贝或者移动原data_dir到新data_dir
  
  ```bash
     # 我使用拷贝风险更小 -a 可以保留原文件的属性
     sudo cp -a /var/lib/mysql /mnt/data/mysql
  ``` 