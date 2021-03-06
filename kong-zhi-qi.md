# 控制器

一个继承\swoft\web\Controller的类既是控制器，控制器有两种注解自动注册和手动注册两种方式。建议使用注解自动注册，方便简洁，维护简单。多次注册相同的路由前者会被后者覆盖。

## 注解自动注册

注解自动注册常用到三个注解@AutoController、@Inject、@RequestMapping.

> **@AutoController**
>
> 已经使用@AutoController，不能再使用@Bean注解。
>
> @AutoController注解不需要指定bean名称，统一类为bean名称
>
> @AutoController\(\)默认自动解析controller前缀，并且使用驼峰格式。
>
> @AutoController\(prefix="/demo2"\)或@AutoController\("/demo2"\)功能一样，两种使用方式。
>
> **@Inject**
>
> 使用和之前的一样
>
> **@RequestMapping**
>
> @RequestMapping\(route="/index2"\)或@RequestMapping\("/index2"\)功能一样两种方式使用，这种默认是支持get和post方式@RequestMapping\(route="/index2", method=RequestMethod::GET\)注册支持的方法
>
> 不使用@RequestMapping或RequestMapping\(\)功能一样，都是默认解析action方法，以驼峰格式，注册路由。

```php
 /**
 * 控制器demo
 *
 * @AutoController(prefix="/demo2")
 *
 * @uses      DemoController
 * @version   2017年08月22日
 * @author    stelin <phpcrazy@126.com>
 * @copyright Copyright 2010-2016 swoft software
 * @license   PHP Version 7.x {@link http://www.php.net/license/3_0.txt}
 */
class DemoController extends Controller
{
    /**
     * 注入逻辑层
     *
     * @Inject()
     * @var IndexLogic
     */
    private $logic;

    /**
     * 定义一个route,支持get和post方式，处理uri=/demo2/index
     *
     * @RequestMapping(route="index", method={RequestMethod::GET, RequestMethod::POST})
     */
    public function actionIndex()
    {
        // 获取所有GET参数
        $get = $this->get();
        // 获取name参数默认值defaultName
        $name = $this->get('name', 'defaultName');
        // 获取所有POST参数
        $post = $this->post();
        // 获取name参数默认值defaultName
        $name = $this->post('name', 'defaultName');
        // 获取所有参，包括GET或POST
        $request = $this->request();
        // 获取name参数默认值defaultName
        $name = $this->request('name', 'defaultName');
        //json方式显示数据


        $this->outputJson("suc");
    }

    /**
     * 定义一个route,支持get,以"/"开头的定义，直接是根路径，处理uri=/index2
     *
     * @RequestMapping(route="/index2", method=RequestMethod::GET)
     */
    public function actionIndex2()
    {
        // 重定向一个URI
        $this->redirect("/login/index");
    }

    /**
     * 没有使用注解，自动解析注入，默认支持get和post,处理uri=/demo2/index3
     */
    public function actionIndex3()
    {
        $this->outputJson("suc3");
    }
}
```

## 手动注册

手动注册常用@Bean、@Inject注解，手动注册还要多一步就是在routes.php里面注册自己的路由规则。

> 手动注册@Bean\(\)只能这样缺省方式。并且不能使用@AutoController注解

```php
/**
 * 控制器demo
 *
 * @Bean()
 *
 * @uses      DemoController
 * @version   2017年08月22日
 * @author    stelin <phpcrazy@126.com>
 * @copyright Copyright 2010-2016 swoft software
 * @license   PHP Version 7.x {@link http://www.php.net/license/3_0.txt}
 */
class DemoController extends Controller
{
    /**
     * 注入逻辑层
     *
     * @Inject()
     * @var IndexLogic
     */
    private $logic;

    /**
     * uri=/demo2/index
     */
    public function actionIndex()
    {
        // 获取所有GET参数
        $get = $this->get();
        // 获取name参数默认值defaultName
        $name = $this->get('name', 'defaultName');
        // 获取所有POST参数
        $post = $this->post();
        // 获取name参数默认值defaultName
        $name = $this->post('name', 'defaultName');
        // 获取所有参，包括GET或POST
        $request = $this->request();
        // 获取name参数默认值defaultName
        $name = $this->request('name', 'defaultName');
        //json方式显示数据


        $this->outputJson("suc");
    }

    /**
     * uri=/index2
     */
    public function actionIndex2()
    {
        // 重定向一个URI
        //        $this->redirect("/login/index");
        $this->outputJson("suc2");
    }

    /**
     */
    public function actionIndex3()
    {
        $this->outputJson("suc3");
    }
}
```

routes.php手动注册路由

```php
// ...

$router->map(['get', 'post'], '/demo2/index', 'app\controllers\DemoController@index');
$router->get('/index2', 'app\controllers\DemoController@index2');
$router->get('/demo2/index3', 'app\controllers\DemoController@index3');
```



