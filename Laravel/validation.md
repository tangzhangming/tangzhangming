## 目录
[Validator非完全用法汇总](#Validator非完全用法汇总)
[FormRequest表单请求类例子](#FormRequest表单请求类例子)
[FormRequest闭包验证器和行内验证器](#FormRequest闭包验证器和行内验证器)
[FormRequest统一处理错误响应](#FormRequest统一处理错误响应)
[验证器错误背包用法](#验证器错误背包用法)
[Precognitive预认知](#Precognitive预认知)

## Validator非完全用法汇总
```PHP
    // 包含文档中没有提到的
    // Illuminate\Support\Facades\Validator
    // Illuminate\Validation\Validator

    $validator = Validator::make($request->all(), $req->all())->validateWithBag('myBag');

    // 返回验证失败的数据 bool, validated()的反逻辑
    $validator->invalid();

    // 获取验证成功的数据 bool, invalid()的反逻辑
    $validator->validated();

    // 确定数据是否通过验证规则 bool, fails()的反逻辑
    $validator->passes();

    // 确定数据是否没有通过验证规则 bool, passes()的反逻辑
    $validator->fails();

    // 验证数据 如果有错抛出错误 array
    // 不想抛出就通过 passes() 和 fails() 自己处理
    // @throws \Illuminate\Validation\ValidationException
    $validator->validate();

    // 返回有效的数据 array
    $validator->valid();

    // 获取失败的验证规则 array
    $validator->failed();

    // 根据闭包向给定字段添加条件
    // 非常有用
    $validator->sometimes($attribute, $rules, callable $callback);

    // 指示验证器在第一个规则失败后停止验证 默认是false
    // 非常有用
    $validator->stopOnFirstFailure($stopOnFirstFailure = true);

    // 获取验证器的消息容器 后面两个是messages的马甲 
    $validator->messages();
    $validator->errors();
    $validator->getMessageBag();

    // 获取要验证的数据
    $validator->attributes(); == getData()

    // 获取给定属性的值
    // 如果你在写自定义规则，一个字段依赖另一个字段的时候这十分有用
    $validator->getValue($attribute);

    // 确定给定属性在给定集合中是否有规则
    $validator->hasRule(string $attribute, string|array $rules);

    // 获取给定属性的规则及其参数
    $validator->getRule(string $attribute, string|array $rules);


    // 将失败的规则和错误消息添加到集合中 void
    $validator->addFailure(string $attribute, string $rule, array $parameters = []);

    // 注册自定义验证器扩展数组
    // addExtension('custom_rule', CustomRule::class);
    $validator->addExtension($rule, $extension)
    $validator->addImplicitExtension($rule, $extension)//隐式扩展
    $validator->addDependentExtension($rule, $extension)
    $validator->addExtensions(array $extensions);
    $validator->addImplicitExtensions(array $extensions);//隐式扩展
    $validator->addDependentExtensions(array $extensions);

    // 注册自定义验证器消息替换器
    $validator->addReplacer($rule, $replacer)

    $validator->setCustomMessages(array $messages)
    $validator->setAttributeNames(array $attributes)

```

## FormRequest表单请求类例子
```PHP
<?php
namespace App\Http\Requests;

use Closure;
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Contracts\Validation\Validator;
use Illuminate\Http\Exceptions\HttpResponseException;

class TestRequest extends FormRequest
{

    /**
     * 验证失败的重定向地址 
     * 4选一设置即可 权重依顺序 也可重写getRedirectUrl()方法
     * 默认重定向回来源页 可在failedValidation()里设置禁止重定向
     */
    protected $redirect;
    protected $redirectRoute;
    protected $redirectAction;
    protected $redirector;

    /**
     * 错误背包
     * 如果你的页面有多个独立的表单 可通过错误背包来区分
     * @link https://learnku.com/docs/laravel/10.x/validation/14856#named-error-bags
     * @var string
     */
    protected $errorBag = 'default';


    /**
     * 表示验证器是否应在第一个规则失败时停止
     * 注意此处是停止所有属性与规则，与停止单个属性的bail规则不同
     * 
     * @var bool
     */
    protected $stopOnFirstFailure = false;

    /**
     * 确定用户是否有权提出此请求
     * 可以在该处做权限判断，比如用户发表帖子，可以在此处判断用户是否有权发帖
     *
     * @return bool
     */
    public function authorize(): bool
    {
        // 例子
        $registerTimestamp = strtotime(Auth::user()->created_at);
        if( (time() - $registerTimestamp) < 86400 ){
            //注册时间不足24小时，禁止发表新帖子
            return false;
        }

        return true;
    }

    // 授权失败处理, 当前类authorize()方法存在且返回 false 时调用此处 可省略
    protected function failedAuthorization()
    {
        throw new \Illuminate\Auth\Access\AuthorizationException;
    }

    /**
     * 要验证的数据，可省略，默认值 request()->all()
     */
    public function validationData()
    {
        return [
            'title'    => '不像我只会心疼哥哥',
            'content'  => '哥哥，你女朋友要是知道我俩吃同一个棒棒糖，你女朋友不会吃醋吧！',
            'password' => '88888888',  
        ];
    }

    /**
     * 验证规则
     */
    public function rules(): array
    {
        return [
            'title'    => 'required|between:8,50',
            'content'  => 'required|min:100',
        ];
    }

    /**
     * 自定义属性别名，可省略
     */
    public function attributes()
    {
        return [
            'title'   => '标题',
            'content' => '内容',
        ];
    }

    /**
     * 自定义错误消息，可省略
     */
    public function messages()
    {
        return [
            'required'         => ':attribute不能为空, 请填写后再提交',
            'title.between'    => '帖子标题长度限制在:min至:max个字之间',
            'password.between' => '密码错误',
        ];
    }

    // 准备验证数据
    protected function prepareForValidation(): void
    {
        $this->merge([
            /**
             * rules验证的是这里你修改过后的数据
             * 'www.domain.com' 变成 'http://www.domain.com' 进行验证，且影响 $this->all() 与 $this->validated() 结果
             */
            'website' => "http://".$this->website,
        ]);
    }

    // 验证完成后对任何请求数据进行规范化 可省略
    protected function passedValidation(): void
    {
        /**
         * 'http://www.domain.com' 变成 'http://www.domain.com?code=123456'
         * 影响 $this->all()、 $this->input('website') 等
         * 不影响 $this->validated() 结果，里面还是 'http://www.domain.com'
         */
        $this->replace([
            'website' => $this->website.'?code=123456'
        ]);
    }

    /**
     *  配置验证器实例。
     *
     * @param  \Illuminate\Validation\Validator  $validator
     * @return void
     */
    public function withValidator(\Illuminate\Validation\Validator $validator)
    {
        // 这里是验证器的后置操作
        $validator->after(function (\Illuminate\Validation\Validator $validator) {

            /**
             * errors 具体内容可以参考 Illuminate\Support\MessageBag
             */
            if( $validator->errors()->isEmpty() ){

                /**
                 * 运行到 isEmpty() 时，表示rules里面的验证规则都通过了
                 * 此时密码只进行了rules里的规则验证，我们需要继续验证密码是否与持久储存相同
                 */
                $password = $this->input('password');
                if( $password !== '123456' ){
                    $validator->errors()->add('password', '数据库比对密码错误');
                } 

            }

        });
    }

    /**
     *
     * 自定义验证失败后的错误处理
     *
     * 必需抛出一个Exception，否则业务代码会继续执行，抛出了Exception则会被框架捕捉处理
     * 直接return返回一个response对象是无效的
     * 
     * 父类 FormRequest 里面默认抛出的是 Illuminate\Validation\ValidationException
     * 如果是表单请求(application/x-www-form-urlencoded)它会重定向回来源页面，并且把错误信息写入flash session，
     * 非表单请求否则返回一个固定格式的json响应
     * 
     * 父类默认的failedValidation不能满足以下需求
     * 1、在实际使用中，有的时候我们想验证失败时始终返回json而不跳转，甚至是xml
     * 2、默认返回的json格式是固定，而我们可能需要自定义一些json字段
     * 
     */
    protected function failedValidation(Validator $validator)
    {
        // 具体内容可以参考 Illuminate\Support\MessageBag 
        $error = $validator->errors();

        // 自定义response对象 Illuminate\Http\Response
        $response = response()->json([
            //...自定义的错误响应内容
            'code'    => 10001,
            'message' => $error->first(),
            'errors'  => $error->errors(),
        ]);

        /**
         * 抛出一个HttpResponseException异常类,
         * 这将阻止验证器调用后的后续代码，直接发送response到浏览器
         */
        throw new HttpResponseException($response);
    }
}

```


## FormRequest闭包验证器和行内验证器
```PHP
<?php

use Illuminate\Support\Facades\Validator;

class TestRequest extends FormRequest{

    public function rules(): array
    {
        return [

            'title' => [
                'required', 
                'string',
                // 闭包验证器写法1 避免rules太臃肿
                Closure::fromCallable([$this, 'validateTitle'])
            ]

            // 闭包验证器写法2
            'content' => ['string', function($attribute, $value, $fail){
                if( ... ){
                    $fail("参数 {$attribute} 不符合要求.");
                }
            }],

            // 使用自定义验证器phoneNumber， 在prepareForValidation里注册
            'mobile' => 'required|phoneNumber',
        ];    
    }

    protected function prepareForValidation(): void
    {
        Validator::extend('phoneNumber', [$this, 'validatePhoneNumber']);
    }

    /**
     * 自定义验证器 phoneNumber
     * 自定义验证器参数比闭包多 可以做过多事情
     */
    public function validatePhoneNumber($attribute, $value, $parameters, $validator)
    {
        // 可以通过validator取到其他字段值
        $areaCode = $validator->getValue('area-code');

        if( $areaCode == '1' ){
            // 美国手机号格式验证

        }elseif( $areaCode == '86' ){
            // 中国手机号格式验证
            if(...){
                $validator->errors()->add($attribute, "{$attribute}不是中国大陆手机号");
            }
        }

        return true;
    }

    // 闭包验证器 没有
    public function validateTitle($attribute, $value, $fail)
    {
        if(...){
            $fail("参数 {$attribute} 不符合要求.");
        }

        return true;
    }

}
```

## FormRequest统一处理错误响应
```PHP
<?php
namespace App\Exceptions;

use Illuminate\Foundation\Exceptions\Handler as ExceptionHandler;
use Throwable;
use Illuminate\Http\Request;
use Illuminate\Validation\ValidationException;

class Handler extends ExceptionHandler
{
    ...

    public function register(): void
    {
        // 为表单验证统一处理返回，表单验证错误将由这里兜底处理
        // 包含没有自定义 failedValidation 的独立验证类和 $request->validate()
        $this->renderable(function (ValidationException $e, Request $request) {
            return response()->json([
                'code'     => 2,
                'message'  => $e->getMessage(),
                'error'    => $e->validator->errors(),
            ], $e->status);
        });


        $this->reportable(function (Throwable $e) {
            //
        });
    }
}

```


## 验证器错误背包用法 
```PHP
    $validator = Validator::make($request->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ]);

    // Illuminate\Support\MessageBag
    if ($validator->fails()) {
        $errors = $validator->errors();

        // 最常用
        $errors->first($key = null, $format = null);
        $errors->get($key, $format = null);
        $errors->all($format = null);   
        $errors->isEmpty();                       // bool 是否没有错误
        $errors->isNotEmpty();                    // bool 是否包含错误
        $errors->count();                         // int  错误总数
        $errors->add($key, $message);             // this 添加错误

        // 非常用
        $errors->keys();                          // 返回包含错误的keys
        $errors->addIf($boolean, $key, $message); // 如果参数1为“true”，则将消息添加到消息包中。
        $errors->isUnique($key, $message);        // 确定key和message的组合是否已经存在
        $errors->merge($messages);                // 将新的消息数组合并到消息包中
        $errors->has($key);
        $errors->hasAny($keys = []);              // 确定是否存在任何给定key的message，可以传入非数组
        $errors->missing($key);                   // 确定是否不存在所有给定key的message
        $errors->unique($format = null);          // 确定是否不存在所有给定key的message
        $errors->forget($key);
        $errors->toArray();
        $errors->toJson();
    }
```

## 独立表单验证类自动调用流程
```
class HomeController extends Controller{

    // FormRequest代替Illuminate\Http\Request作为控制器操作的request参数注入
    public function myaction(TestRequest $request)
    {
        // 你会发现使用独立验证类时，不需要fails()
        // 验证通过了
        return $request->validated();
    }   
    
}
```
1. 实现自定义验证类 ```MyRequest```
2. ```MyRequest``` 继承 ```Illuminate\Foundation\Http\FormRequest```
3. ```FormRequest``` 中Trait了 ```ValidatesWhenResolvedTrait```
4. ```ValidatesWhenResolvedTrait``` 的 ```validateResolved``` 方法调用了 ```MyRequest->fails()```, 失败时调用 ```failedValidation```方法
3. ```Illuminate\Foundation\Providers\FormRequestServiceProvider``` 中启用了你的 ```MyRequest```

## Precognitive预认知 
浏览器头包含Precognition-Validate-Only时，laravel响应header自动加入Precognition-Success=true
可以使用 $this->isPrecognitive() 判断
```
    public function rules(): array
    {
        return [
            'password' => ['required',
                $this->isPrecognitive()
                    ? Password::min(8)
                    : Password::min(8)->uncompromised(),
            ],
        ];
    }
```
