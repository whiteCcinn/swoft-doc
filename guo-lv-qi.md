# 过滤器

filter一般用户请求过滤处理，比如全局参数验证、签名验证、登陆验证等等。目前支持三种方式匹配过滤器，类似于spring mvc中的过滤器。

* 后缀名称匹配，比如匹配 .html/.php 后缀路径
* 路径匹配，比如/a/b/\*,匹配/a/b/下所有路径
* 精确完整路径匹配，比如精确匹配 /user/login

## 定义filter

定义一个Filter很简单, 只需基础swoft\filter\Filter类，实现doFilter和denyFilter两个方法。doFilter实现filter处理逻辑，当filter逻辑验证不通过时，调用denyFilter，并返回false终止执行，反之验证成功，调用FilterChain-&gt;doFilter执行下一个过滤器。特别说明下，如果需要filter不通过的时候，直接返回用户数据，可以直接使用Response对象实现。

```php
use swoft\filter\Filter;
// ...

class LoginFilter extends Filter
{
    public function doFilter(Request $request, Response $response, FilterChain $filterChain, int $currentIndex = 0)
    {
        // 逻辑验证
        $uid = $request->getParameter('uid');
        if($uid != 6){
            // 逻辑验证不通过
            $this->denyFilter($request, $response);
            return false;
        }
        return $filterChain->doFilter($request, $response, $filterChain, $currentIndex);
    }

    public function denyFilter(Request $request, Response $response)
    {
        // 直接返回数据给用户
        $response->setResponseContent(json_encode(array('status' => 403, 'msg' => 'need login!')));
        $response->setFormat(Response::FORMAT_JSON);
        $response->send();
    }
}
```

## 配置filter

配置一个filter也很简单，只需在base.php里面配置一个Filter bean,并且在filter Bean里面引用即可。uriPattern对应上面说的三种过滤规则。

```php
return [

    // ...
    
    'commonParamsFilter' => [
        'class'      => \app\beans\filters\CommonParamsFilter::class,
        'uriPattern' => '/*', // 目录匹配
    ],
    'loginFilter'        => [
        'class'      => \app\beans\filters\LoginFilter::class,
        'uriPattern' => '/index/login', // 精确匹配
    ],
    'filter'             => [
        'filters' => [
            '${commonParamsFilter}',
            '${loginFilter}',
        ],
    ],    
    
    // ...
    
];
```



