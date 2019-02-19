# 设计模式
---
#### 提高代码复用率、可维护性

## 工厂模式

当修改一个类的名字或者方法之后，在调用这个类的所有的代码都要修改，维护性太低。  
所以使用工厂类或者工厂方法生成对象，而不是在代码中直接new。【工厂模式-&gt;服务容器 了解一哈】

```php
    /**
    * 类
    */
    class A()
    {
        // TODO
    }

    /**
    * 工厂类
    */
    class Factory()
    {
        /**
        * 创建方法
        */
        public static function createA(): A
        {
            $a = new A();
            return $a;
        }
    }

    /**
    * 创建
    */
    $aa = Factory::createA();

    // TODO
```

## 依赖注入模式

控制反转（IoC）的实现，当一个类调用另一个类时，对象通过参数在需要的地方注入，提高扩展性。

```php
    /**
    * 被调用的类
    */
    class A()
    {
        private $tag;

        public function __construct(string $tag)
        {
            $this->tag = $tag;
        }
    }

    /**
    * 调用
    */
    class B()
    {
        private $a;

        public function __construct(A $a)
        {
            $this->a = $a
        }
    }

    /**
    * 测试
    */
    class Test()
    {
        $a1 = new A('aa');
        $b1 = new B($a1);

        // TODO
    }
```

## 适配器模式
将一个类的接口转换成可应用的兼容接口。适配器使原本由于接口不兼容而不能一起工作的那些类可以一起工作。


## 观察者模式

当对象的状态发生变化时，所有依赖于它的对象都得到通知并被自动更新。它使用的是低耦合的方式，非侵入式的通知与更新机制。  
场景:一个事件发生后，要执行一连串更新操作。传统的编程方式，就是在事件的代码之后直接加入处理的逻辑。当更新的逻辑增多之后，代码会变得难以维护。这种方式是耦合的，侵入式的，增加新的逻辑需要修改事件的主体代码。

PHP 已经定义了2个接口用于快速实现观察者模式：SplObserver 和 SplSubject。

```php
    /**
     * User 实现观察者模式 (称为主体)，它维护一个观察者列表，
     * 当对象发生变化时通知  User。
     */
    class User implements \SplSubject
    {
        private $email;

        /**
        * 观察者列表
        * @var \SplObjectStorage 
        */
        private $observers;

        public function __construct()
        {
            $this->observers = new \SplObjectStorage();
        }

        /**
        * 加入观察者列表
        * @var \SplObjectStorage 
        */
        public function attach(\SplObserver $observer)
        {
            $this->observers->attach($observer);
        }

        /**
        * 移除观察者列表
        * @var \SplObjectStorage 
        */
        public function detach(\SplObserver $observer)
        {
            $this->observers->detach($observer);
        }

        /**
        * 发生的更新事件
        */
        public function changeEmail(string $email)
        {
            $this->email = $email;
            $this->notify();
        }

        /**
        * 通知观察者做出更新
        */
        public function notify()
        {
            /** @var \SplObserver $observer */
            foreach ($this->observers as $observer) {
                $observer->update($this);
            }
        }
    }

    /**
    * 观察者
    */
    class UserObserver implements \SplObserver
    {
        /**
        * @var User[]
        */
        private $changedUsers = [];

        /**
        * 它通常使用  SplSubject::notify()  通知主体
        *
        * @param \SplSubject $subject
        */
        public function update(\SplSubject $subject)
        {
            $this->changedUsers[] = clone $subject;
        }

        /**
        * @return User[]
        */
        public function getChangedUsers(): array
        {
            return $this->changedUsers;
        }

    }

    /**
    * 测试
    */
    $ob = new UserObserver();
    $user = new User();
    
    $user->attach($ob)
    $user->changeEmail('c@gmail.com');
    
    var_dump($ob->getChangedUsers());
```


