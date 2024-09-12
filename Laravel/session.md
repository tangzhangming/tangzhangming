## SESSION

$session = $request->session();



// 增改
$session->put('key', 'value');
$session->push('user.teams', 'developers');
$session->increment('count');     // 递增
$session->decrement('count');     // 递减

// 删除
$session->pull('key', 'default'); // 获取并删除
$session->forget('key');
$session->forget(['name', 'status']);
$session->flush();

// 读取
$session->all();
$session->get('key', 'default');  //获取
$session->pull('key', 'default'); //获取并删除
$session->only(array $keys);
$session->except(array $keys);


// 判断
$session->has('key');
$session->missing('key'); // 是否不存在
$session->exists('key');  // 存在即使为null

// 闪存
$session->flash('status', 'Task was successful!');
$session->reflash();
$session->keep(['username', 'email']);
$session->now('status', 'Task was successful!');


### 其他

$session->getId();
$session->setId();
$session->getName();
$session->setName();
$session->start();
$session->isStarted();


// 重新生成csrf_token
$session->regenerateToken();
$session->token();

// 重新生成 Session ID
$session->regenerate();

// 重新生成 Session ID 并同时删除所有 Session 里的数据
$session->invalidate();


// 确定这是符合格式的session_id
$session->isValidId();

// 生产session_id
$session->generateSessionId();

// 从会话中获取上一个url
$session->previousUrl();




