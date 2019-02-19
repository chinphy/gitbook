# 设计模式
#### 提高代码复用率、可维护性

## 工厂模式
当修改一个类的名字或者方法之后，在调用这个类的所有的代码都要修改，维护性太低。
所以使用工厂类或者工厂方法生成对象，而不是在代码中直接new。【工厂模式->服务容器 了解一哈】

```PHP
    class A()
    {
        //TODO
    }
    
    class Factory()
    {
        public static function createA()
        {
            $a = new A();
            return $a
        }
    }
    
    $aa = Factory::createA();
    //TODO
   
```

