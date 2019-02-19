# 设计模式
#### 提高代码复用率、可维护性

## 工厂模式
当修改一个类的名字或者方法之后，在调用这个类的所有的代码都要修改，维护性太低。
所以使用工厂类或者工厂方法生成对象，而不是在代码中直接new。【工厂模式->服务容器 了解一哈】

```php

    // 类
    class A()
    {
        // TODO
    }
    
    // 工厂类
    class Factory()
    {
        // 创建方法
        public static function createA()
        {
            $a = new A();
            return $a;
        }
    }
    
    // 创建
    $aa = Factory::createA();
    
    // TODO
   
```
## 依赖注入模式
控制反转（IoC）的实现，当一个类调用另一个类时，对象通过参数在需要的地方注入，提高扩展性。

```php

    // 被调用的类
    class A()
    {
        private $tag;
        
        public function __construct(string $tag)
        {
            $this->tag = $tag;
        }
    }
    
    // 调用
    class B()
    {
        private $a;
        
        public function __construct(A $a)
        {
            $this->a = $a
        }
    }
    
    // 测试
    class Test()
    {
        $a1 = new A('aa');
        $b1 = new B($a1);
        
        // TODO
    }

```

## 单例模式


## 适配器模式
## 策略模式
## 观察者模式
## 装饰器模式

