## 表单请求(FormRequest)独立验证类完整例子
```PHP
<?php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Contracts\Validation\Validator;
use Illuminate\Http\Exceptions\HttpResponseException;

class TestRequest extends FormRequest
{

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

            // rule可以是|分割的字符串，也可以是一维数组
            'website'  => ['required', 'url'],

            // 某些非内置的非通用特殊验证需求，可以在闭包里完成
            'test' => ['size:5', function($attribute, $value, $fail){
                if( $value !== '12345' ){
                    $fail("参数 {$attribute} 不符合要求.");
                }
            }]

            // 假设我们为了用户安全，发帖时必须提交密码二次验证
            // 可以使用闭包完成数据库对比密码，但是我们希望其他参数都验证通过后再验证，节省数据库查询，所以我们在after中进行数据库密码比对
            'password' => 'required|between:6,20',
        ];
    }

    /**
     * 自定义属性别名，可省略
     */
    public function attributes()
    {
        return [
            'title'   => '帖子标题',
            'content' => '帖子内容',
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
            'content.min'      => '帖子内容至少需要100个字',
            'password.between' => '密码错误',
        ];
    }


    // 授权失败处理, 当前类authorize()方法存在且返回 false 时调用此处 可省略
    protected function failedAuthorization()
    {
        throw new \Illuminate\Auth\Access\AuthorizationException;
    }
 
    // 数据预处理 可省略
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
     * 行内验证器
     * 也许你在yii2和thinkphp中经常这样用且十分好用, 但是laravel中无法实现，请参考验证器闭包文档代替
     * 
     * laravel验证器闭包
     * @link https://learnku.com/docs/laravel/10.x/validation/14856#bf0dbb
     *
     * yii行内验证器     
     * @link https://www.yiiframework.com/doc/guide/2.0/zh-cn/input-validation#inline-validators
     * 
     * tp自定义验证规则  
     * @link https://doc.thinkphp.cn/v8_0/validator.html
     */
    public function validateTitle($attribute, $value, $fail)
    {
        return ;
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


## 验证器错误背包用法 Illuminate\Support\MessageBag
```PHP
    $validator = Validator::make($request->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ]);

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
