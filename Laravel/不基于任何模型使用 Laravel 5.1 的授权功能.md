 不基于任何模型使用 Laravel 5.1 的授权功能
  From:<http://laravelacademy.org/post/1354.html>
     Laravel 5.1.11 引入了一个新特性 —— 授权机制，
该特性被设计为定义一系列与模型相关联的策略来实现授权。
但有时候我们想要定义一个检测更高级别权限的策略，在该场景下不基于任何模型
比如文章更新权限基于Post模型。下面这段代码即可实现该需求：

1、创建策略类
<pre>
class AdminPolicy
{
    public function managePages($user)
    {
        return $user->hasRole(['Administrator', 'Content Editor']);
    }
}
</pre>
2、在AuthServiceProvider中定义权限
<pre>
    /**
    * 注册认证 / 授权服务.
    *
    * @param \Illuminate\Contracts\Auth\Access\Gate $gate
    * @return void
    */
    public function boot(GateContract $gate)
    {
        foreach (get_class_methods(new \App\Policies\AdminPolicy) as $method) {
            $gate->define($method, "App\Policies\AdminPolicy@{$method}");
        }
    
        $this->registerPolicies($gate);
    }
</pre>
3、 使用（控制器/Blade/Eloquent）
<pre>
  $this->authorize('managePages'); // 在控制器中使用
  @can('managePages') // 在Blade视图中使用
  $user->can('managePages'); // 通过Eloquent使用
</pre>