# 插件化
插件在根目录下的addons中有示例可以参考。
需结合项目：[ThinkYXC-CMS](https://github.com/llllyang123/thinkyxc)使用，借鉴于[think-addons](https://github.com/zz-studio/think-addons)，使插件更加容易管理、升级等

# 教程
## 创建插件
> 创建的插件可以在view视图中使用，也可以在php业务中使用

安装完成后访问系统时会在项目根目录生成名为`addons`的目录，在该目录中创建需要的插件。

下面写一个例子：

### [](https://github.com/zz-studio/think-addons#创建test插件)创建test插件

> 在addons目录中创建test目录

### [](https://github.com/zz-studio/think-addons#创建钩子实现类)创建钩子实现类

> 在test目录中创建 Plugin.php 类文件。注意：类文件首字母需大写
~~~
<?php
namespace addons\test;	// 注意命名空间规范

use think\Addons;

/**
 * 插件测试
 * @author byron sampson
 */
class Plugin extends Addons	// 需继承think\Addons类
{
    // 该插件的基础信息
    public $info = [
        'name' => 'test',	// 插件标识
        'title' => '插件测试',	// 插件名称
        'description' => 'thinkph6插件测试',	// 插件简介
        'status' => 0,	// 状态
        'author' => 'byron sampson',
        'version' => '0.1'
    ];

    /**
     * 插件安装方法
     * @return bool
     */
    public function install()
    {
        return true;
    }

    /**
     * 插件卸载方法
     * @return bool
     */
    public function uninstall()
    {
        return true;
    }

    /**
     * 实现的testhook钩子方法
     * @return mixed
     */
    public function testhook($param)
    {
		// 调用钩子时候的参数信息
        print_r($param);
		// 当前插件的配置信息，配置信息存在当前目录的config.php文件中，见下方
        print_r($this->getConfig());
		// 可以返回模板，模板文件默认读取的为插件目录中的文件。模板名不能为空！
        return $this->fetch('info');
    }

}
~~~
### 创建插件配置文件

> 在test目录中创建config.php类文件，插件配置文件可以省略。

~~~html
<?php
return [
    'display' => [
        'title' => '是否显示:',
        'type' => 'radio',
        'options' => [
            '1' => '显示',
            '0' => '不显示'
        ],
        'value' => '1'
    ]
];
~~~

###创建插件状态文件
~~~
{
    "name": "tese",
    "title": "测试插件",
    "description": "测试插件",
    "status": 0,
    "author": "爱途平台",
    "version": "1.0",
    "demo_url": "http://www.aitu666.cn",
    "author_url": "http://www.aitu666.cn",
    "hasAdmin": "0"
}
~~~
hasAdmin为是否有后台文件，如果有会自动匹配跳转后台文件页面按钮，0=没有，1=有
更多详情参考根目录下的示例插件
### [](https://github.com/zz-studio/think-addons#创建钩子模板文件)创建钩子模板文件

> 在test->view目录中创建info.html模板文件，钩子在使用fetch方法时对应的模板文件。

~~~html
<h1>hello tpl</h1>

如果插件中需要有链接或提交数据的业务，可以在插件中创建controller业务文件，
要访问插件中的controller时使用addon_url生成url链接。
如下：
<a href="{:addons_url('Action/link')}">link test</a>
或
<a href="{:addons_url('test://Action/link')}">link test</a>
格式为：
test为插件名，Action为controller中的类名[多级控制器可以用.分割]，link为controller中的方法
~~~

### [](https://github.com/zz-studio/think-addons#创建插件的controller文件)创建插件的controller文件

> 在test目录中创建controller目录，在controller目录中创建Index.php文件 controller类的用法与tp6中的controller一致

~~~html
<?php
namespace addons\test\controller;

class Index
{
    public function link()
    {
        echo 'hello link';
    }
}
~~~

## [](https://github.com/zz-studio/think-addons#使用钩子)使用钩子

> 创建好插件后就可以在正常业务中使用该插件中的钩子了 使用钩子的时候第二个参数可以省略

### [](https://github.com/zz-studio/think-addons#模板中使用钩子)模板中使用钩子

~~~html
<div>{:hook('testhook', ['id'=>1])}</div>
~~~

### [](https://github.com/zz-studio/think-addons#php业务中使用)php业务中使用

> 只要是thinkphp6正常流程中的任意位置均可以使用

~~~html
hook('testhook', ['id'=>1])
~~~

### [](https://github.com/zz-studio/think-addons#插件公共方法)插件公共方法

~~~html
/**
 * 处理插件钩子
 * @param string $event 钩子名称
 * @param array|null $params 传入参数
 * @param bool $once 是否只返回一个结果
 * @return mixed
 */
function hook($event, $params = null, bool $once = false);

/**
 * 读取插件的基础信息
 * @param string $name 插件名
 * @return array
 */
function get_addons_info($name);

/**
 * 获取插件Plugin的单例
 * @param string $name 插件名
 * @return mixed|null
 */
function get_addons_instance($name);

/**
 * 插件显示内容里生成访问插件的url
 * @param $url 在插件控制器中可忽略插件名，在非插件中生成时需指定插件名。例：插件名://控制器/方法
 * @param array $param
 * @param bool|string $suffix 生成的URL后缀
 * @param bool|string $domain 域名
 * @return bool|string
 */
function addons_url($url = '', $param = [], $suffix = true, $domain = false);
~~~
## 插件目录结构

### [](https://github.com/zz-studio/think-addons#最终生成的目录结构为)最终生成的目录结构为

~~~html
www  WEB部署目录（或者子目录）
├─addons           插件目录
├─app           应用目录
│  ├─controller      控制器目录
│  ├─model           模型目录
│  ├─ ...            更多类库目录
│  │
│  ├─common.php         公共函数文件
│  └─event.php          事件定义文件
│
├─config                配置目录
│  ├─app.php            应用配置
│  ├─cache.php          缓存配置
│  ├─console.php        控制台配置
│  ├─cookie.php         Cookie配置
│  ├─database.php       数据库配置
│  ├─filesystem.php     文件磁盘配置
│  ├─lang.php           多语言配置
│  ├─log.php            日志配置
│  ├─middleware.php     中间件配置
│  ├─route.php          URL和路由配置
│  ├─session.php        Session配置
│  ├─trace.php          Trace配置
│  └─view.php           视图配置
│
├─view            视图目录
├─route                 路由定义目录
│  ├─route.php          路由定义文件
│  └─ ...   
│
├─public                WEB目录（对外访问目录）
│  ├─index.php          入口文件
│  ├─router.php         快速测试文件
│  └─.htaccess          用于apache的重写
│
├─extend                扩展类库目录
├─runtime               应用的运行时目录（可写，可定制）
├─vendor                Composer类库目录
├─.example.env          环境变量示例文件
├─composer.json         composer 定义文件
├─LICENSE.txt           授权说明文件
├─README.md             README 文件
├─think                 命令行入口文件
~~~