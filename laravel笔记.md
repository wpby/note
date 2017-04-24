1. 在\web\config 目录下admin_menus.php增加菜单
2. 样式在views/admin/issues/show.blade.php
3. 设置 diffForHumans() 的语言呢？如果做中文站，肯定是希望展示为"x 分钟之前",而不是"x minutes ago"可以在app/Providers/AppServiceProvider.php的boot()方法加上：\Carbon\Carbon::setLocale('zh');
4. 数据库迁移 php artisan migrate
5. Once the middleware has been defined in the HTTP kernel, you may use the middleware key in the route options array:
`
    Route::get('admin/profile', ['middleware' => 'auth', function () {
        //
    }]);
`
增加上middleware  因为5.2把 ->withErrors($validator)会将验证错误信息存放到一次性session中，以便在视图中可以通过$errors变量访问。 去掉了

6. 数据库迁移注意表名都是复数,即加个s
    * 创建tasks表 使用mask:migration命令为tasks 生成一个新的数据库迁移
    php artisan make:migration create_tasks_table --create=tasks 生成的新迁移文件位于database/migrations目录下。这个命令已经添加了自增id和时间戳，然后根据需求编辑增加更多的字段到任务表 
    * 运行迁移使用 php artisan migrate

7. laravel安装并制定版本 进入安装目录 在cmd 命令下执行命令 composer create-project laravel/laravle=5. 1. * laravle5. 1 --prefer-dist  其中laravel/laravle=5. 1. * 为指定下载laravel5. 1版本 laravel5. 1为想要新建项目的文件名名字

8. 使用命令创建model,为tasks表创建模型，使用以下命令, php artisan make:model Task会在app目录下生成,会自动对应tasks表

9. 路由配置在app/http下面, 控制器也在app/controller下面 。php artigsan make:migration生成的迁移文件在 database/migrations下面。 视图页面在resources/views下面， 项目根目录是public，model在app/ 目录下 但也可以更改 

10. model之间的关联,甩hasMany和belongsTo进行关联 比如users表 对应多个tasks
`   
    class User extends Model implements AuthenticatableContract,AuthorizableContract,CanResetPasswordContract
    {
        //任务
        public function tasks(){
            return $this->hasMany(Task::class);
        }
    } 
`
    上面不需要用use app\Task; 在Task模型里面 
`
    class Task extends Model{
        public function user()
        {
            return $this->belongsTo(User::class);
        }
    } app/Http/Controllers/AuthController
`

11. 新安装的laravel应用中已经包含了app/Http/Controllers/AuthController这个控制器,该控制器中使用了一个特殊的AuthenticatesAndRegistersUsers trait,而这个trait 中包含了用户注册登录所必须的相关逻辑

12. 注意布局中的@yield('content')部分，这是一个blade指令，用于指定继承布局的子页面在这里注入自己的内容。

13. 可以在控制器中验证数据 
`
    public function store(Request $request){
        $this->validate($request,[
            'name'=>"required|max:255",
        ]);
    } 
`

14. Html表单只支持post和get两种请求方式,因为需要使用某种方式伪造delete请求

15. 使用blade模板引擎防止csrf攻击{!! csrf_field() !!} 

16. 路由跳转 return redirect()->route('academy',['id'=>1]);

17. 命令空间，routes.php中的定义的控制器位于App\HttpControllers命令空间下，所以如果要指定命名空间，只需要指定App\Http\Contollers之后的部分即可。例如:Route::group([]);

18. 创建迁移1.php artisan make:migration 2. php artisan migrate 

19. 增加字段比如在product表中增加change_max字段
`    
	php artisan make:migration add_change_max_colunmn_to_product_table --table=products

    php artisan make:migration change_title_colunmn_to_review_question_table --table=review_questions

    php artisan make:migration rename_group_to_tag_id_questionnaire_question_table --table=questionnaire_questions
`
	####在新增加的迁移中up方法和down方法中增加下面的代码
`
    public function up()
    {
        Schema::table('products', function (Blueprint $table) {
            $table->Integer('change_max')->default(0)->comment('可调课人数');
        });
    }
`
`
    public function down()
    {
        Schema::table('products', function (Blueprint $table) {
            $table->dropColumn('change_max');
        });
    }
`
    ####在执行php artisan migrate 命令

20. 格式化时间$this->_view['str_time'] = \TimeUtil::sec_to_time($time);

21. 增加一个字段后想要在保存后在填充,例如Openclasses表中的room_id字段,在Model\Openclass.php模型里面增加
	//可填充的
    protected $fillable = array('room_id');即可

22. $this->model->where('id',$id)->whereRaw('student_max > students')->get()->isEmpty()
根据教室id 查询所有student_max 大于students的记录  想使用where 必须有一个变量值

23. MethodNotAllowedHttpException报错,是因为路由不对,很可能是method=put的原因 

24. 获取新增id (1)DB::insertGetId(); (2)使用的是$this->model->save()方法, 直接对象->id 就是新增id

25. ctrl+shift+F 可以查询函数在项目中所有使用这个函数的文件,ctrl+p可以查询文件

26. 删除数据，可以使用数据的事物 
`
    \DB::beginTransaction();

    OpenclassDetail::where('openclass_id', $id)->delete();
    $this->model->where('id', $id)->delete();

    \DB::commit();
`

27. 使用dd(\DB::getQueryLog()); 可以显示查询的mysql 语句;

28.  在后台的voucher/create 页面{!! Form::text('total', null, ['class' => 'spinbox-input form-control text-center spinner']) !!}  输入框为数字并且数字不为负数。 并在下面加上
`
    @section('pagescripts')
        @if($can_edit)
            {!! HTML::script('assets/ace/pages/openclass_edit. js?v=2016041401') !!}
        @endif
        <script type="text/javascript">
            $(function(){
                $('. spinner'). ace_spinner({
                    min: 0,
                    step: 1,
                    max: 1000000,
                    btn_up_class: 'btn-info',
                    btn_down_class: 'btn-info'
                });
            })
        </script>
    @stop
`
29. public $incrementing = false;

30. $now = Carbon::now()->addHours(2)->toDateTimeString();  计算当前时间2个小时后

31. $date='2016-05-02';  时间转换
	$dt_start = new Carbon($date);
    $dt_end = $dt_start->copy()->addDay();

32. resources/macros
    $this->_view['lev']=array_get(\Config::get('base. class_lv'),$student->level_id);

33.  字符串模糊查询
$where[] = ['realname', 'like', '%'. $name. '%'];

34. laravle页面之间进行引用页面,并传递变量。 
`
	@include('Student::course. item')

  	"items" : ['source', 'undo', 'redo', 'table', 'code']
`

35. laravel 改变数据库字段
`    
    Schema::table('review_questions', function (Blueprint $table) {
        $table->text('title')->nullable(null)->comment('题目')->change();
    });

    Schema::table('review_questions;', function (Blueprint $table) {
        $table->string('title',500)->nullable(null)->comment('题目');
    });
`

36. (new Carbon($next_class['start_at']))->toTimeString(); 转换为小时分钟秒

37. $user_id = \Auth::user()->id; 获取当前登录用户id.$user_name = \Auth::user()->usernmae; 获取当前登录者用户名

38. private $img_path = 'http://115. 28. 244. 55:8085/upload';
	$data[0]['link_to'] = preg_replace('/(?<=img src)(\W+)\/upload?/i', "$1". $this->img_path, $data[0]['link_to']);

39. laravel 使用 renameColumn 函数重命名一个字段：
`    
    Schema::table('users', function($table){
        $table->renameColumn('from', 'to');
    });
`

40. 路由怎么修改 都提示不对, 可能ajax 请求的时候token 没加 

41. 如果没有则新建,
`	
	TeacherBank::firstOrNew(['teacher_id'=>$teacher_id]);
`
	####并且在 model 里面设置可填充 
`	
	protected $fillable = ['teacher_id'];
`
`
	public function test()
	{
		$query = $this->model->with(['openclass']);
	    $openclasses = Openclass::where('level', $openclass_level)
	        ->lists('id')->all();
	    if (empty($openclasses)) {
	        return [];
	    }
	
	    $teachers = Teacher::whereNested(function ($query) use ($filtersTeacher) {
	        foreach ($filtersTeacher as $key => $value) {
	            $query->where($value[0], $value[1], $value[2]);
	        }
	    })->lists('id')->all();
	    if (empty($teachers)) {
	        return [];
	    }
	    $query->whereIn('teacher_id', $teachers);
	}
`

42. {{env('APP_CDN')}} cdn 加速
43. Request::getRequestUri() 获取当前url 并且带参数

44. 弹框页面有缓存记得在js 加上这句话
`
    // cleanup the content of the hidden remote modal because it is cached
    $('#myModal'). on('hide. bs. modal', function (e) {
        $(this). removeData('bs. modal');
    });
`
45. 文件上传控制器为admin/uploadController控制器

46. StudentLevel 
    const TYPE_NAMES = [
       '1' => '长期班',
       '2' => '美教班',
       '5' => '厉步',
       '8' => '新概念'
   ];

47. linux 时间同步 /usr/sbin/ntpdate 0. cn. pool. ntp. org或者 sudo ntpdate time. windows. com

48. laravel 修改文件的时候 用composer dump-autoload 更新下laravel 项目vendor文件夹下面的composer缓存文件

49. 安装robomongo 连接不上mongod 数据库 修改mongod. conf中bindIp:127. 0. 0. 1改成0. 0. 0. 0 重启mongod

50. laravel  Seeder数据填充器工具    
    php artisan db:seed   database\seeds 所有表填充数据
    php artisan db:seed --class=ArticleTableSeeder 单个表填充数据

51. App\User::class 这个参数代表User这个model

52. php artisan jwt:generate 生成jwt秘钥

53. 如果站点在国内，建议使用百度静态资源库的Bootstrap，代码如下：
    <link rel="stylesheet" href="http://apps. bdimg. com/libs/bootstrap/3. 3. 4/css/bootstrap. min. css">

54. array_slice(config('base. class_lv'),10),配置文件从第10个开始

55. Laravel  chunk(200) 只取200条, 如果总共有200条以上数据在使用命令文件的时候要多执行几遍命令

56. 命令文件protected $signature = 'update:openclassesLevel'; 在目录使用php artisan update:openclassesLevel    signature(签名; 署名; 识别标志，鲜明特征; [医] 药的用法说明)

57. 创建一个原生表达式 可以使用 DB::raw方法：

    Openclass::where('id', $this->model->openclass_id)->update([
        'students' => DB::raw('students - 1')
    ]);
`
    select 1 from orders where orders. user_id = users. id相等于
`
    DB::table('orders')->select(DB::raw(1))
        ->from('orders')
        ->whereRaw('orders. user_id = users. id');
` 
select 1 其实没什么特别的意思，就是select 一个指定的值，因为我的目地是判断是否有存在，所以不需要返回任何字段信息。写select 1比返回字段信息效率更高 比方说:select 1 from employee, 表employee里有6行数据，因为有6条记录，所以是6个1啊

    php artisan make:migration create_paper
    php artisan make:migration create_papers_table --table=papers

58. $table->decimal('difficulty', 10, 2)->default(0)->comment('试卷难度'); 
    迁移文件  数据库字段两个小数点。
59. replicate() 快速复制数据 Article::find(1)->replicate()->save();
    $article = Article::find(1)->replicate();
    $article->title = 'Laravel 复制数据并修改标题';
    $article->save();

60. html 一行代码实现页面跳转 <meta http-equiv="refresh" content="1;URL=http://www. xwcms. net">

61. MassAssignmentException laravel  在相应model 设置protected $fillable = ['user_id', 'paper_id', 'operation'];

62. //设置cookie 
    return back()->withCookie('lottery_v2_liked_'. $student_id. '_'. $gift->id, 1, 60*24*30);

63. 判断指定日期是否过去
` 
    $start = '010010000';
    if (date('mdHi') >= $start) {
        dd('过去了');
    } else {
        dd('还没到');
    }
`
64.  获得本周周末日期: 
    $dt_start = Carbon::now();   $dt_end = $dt_start->endOfWeek();

65. php 去掉字符串的最后一个字符 
    $str = "1,2,3,4,5,6,"; 
    $newstr = substr($str,0,strlen($str)-1); 
    echo $newstr;  //echo 1,2,3,4,5,6

66. laravel5.3版本中lists换成pluck 

67. 使用mysql语句。
    return PaperQuestion::select(\DB::raw('count(*) as nums, question_type'))
            ->where('paper_id', $paper_id)->groupBy('question_type')->get();

68. angular. js 下拉框是数组时候，比如这样
    'paper_type_arr' => [
        ['type' => '0', 'desc' => "全部"],
        ['type' => '1', 'desc' => "预习"],
    ]
    $scope. paper_type_arr = paper_type_arr;

    $scope. type = $scope. paper_type_arr[0]. type; (这样就可以了)

    <select class="form-control" ng-model="type"
    ng-options="types. type as types. desc for types in paper_type_arr"
    ng-change="selectType()">
    </select>

69. var num = document. getElementById('num'). value;
    alert(~~num);  去掉开头的0

70. php artisan make:auth 自动实现登陆 注册

71. postman中headers可以填充token。  (Authorization: Bearer空格你获取的token)

72. POST方式：HOST/auth/login body参数form-data  mobile :13782341223 password:111111 
就可以获取token

73. 修改Homestead. yaml文件后，需要运行 vagrant provision  重启重新启动 Homestead。

74. cmd进入redis目录 输入redis-server.exe  redis.conf 启动

75. cmd进入mongodb\bin\目录输入mongod --dbpath D:\Mongodb\data 启动

76. git 提交步骤 (1). git status 查看状态  (2). git add 文件 增加文件;(3). git commit -m " 注释1.增加了评论……";(4). git push  
    有冲突 用git pull 解决冲突,git branch 查看分支

77. 新建分支 需要 1. git clone 网址 2.  拉取core分支代码  3. composer  dumpautoload
    4.  配置env 文件5. composer update 

78. composer 下载东西慢 修改composer. json文件 添加到最后
    "repositories": [{
        "type": "composer",
        "url": "https://packagist. phpcomposer. com"
    }, {
        "packagist": false
    }]

79. json_encode($arr, JSON_UNESCAPED_UNICODE); 数组转json时候, 不转义中文

80. json_decode($bak_data, true); 对象json解析为数组

81. $("span[id^='grade'"). parent(). removeClass('active');
匹配id以 index开头的 div。[attribute^=value]：匹配给定的属性是以某些值开始的元素。

82. PSR-0 自动加载 PSR-1 定义基本代码规范 PSR-2 代码样式 PSR-3 日志接口 
PSR-4 如何指定文件路径从而自动加载类定义，同时规范了自动加载文件的位置  。这个乍一看和PSR-0重复了，实际上，在功能上确实有所重复 PSR-4和PSR-0最大的区别是对下划线（underscore)的定义不同。PSR-4中，在类名中使用下划线没有任何特殊含义。而PSR-0则规定类名中的下划线_会被转化成目录分隔符。 

83. ng-class="{true: 'active', false: ''}[{{key}} == 10]" ng-class 样式

84. ng-if 不能跟ng-model 一块使用 最好用ng-show替换

85. homestead登陆mysql :mysql -h192. 168. 10. 10 -uhomestead -p   密码：secret

86. angular.js2.0 npm start 启动失败 修改package. json 
"start": "tsc && concurrently \"tsc -w\" \"lite-server\" ",
为："start": "concurrently \"npm run tsc:w\" \"npm run lite\" ",

87. angular.js2.0 单向绑定： <input value="{{hero. name}}">双向绑定绑定数据 <input [(ngModel)]="hero. name" placeholder="输入姓名">

88. js获取php数组 var s = {!! json_encode(config('questions. question_tag')) !!}

89. 创建命令文件Artisan 控制台命令 php artisan make:console HelloLaravelAcademy --command=laravel:academy, 执行完成后，会在app/Console/Commands目录下生成一个HelloLaravelAcademy. php文件.  在运行命令前需要将其注册到App\Console\Kernel的$commands属性中：
protected $commands = [
     . . .   //其他命令类
     \App\Console\Commands\HelloLaravelAcademy::class
];
在控制台运行如下Artisan命令：
php artisan laravel:academy(来自于HelloLaravelAcademy. php中protected $signature = 'laravel:academy';) $signature即为在控制台执行的命令名

90. 数据库报错日志try catch日志\Log::error('------->addReview Exception# '.$e->getMessage());

91. bootstrap-datepicker限定可选时间范围, startDate: '2017-01-10', endDate : new Date(),//最大日期

92. 修改composer 文件后 记得composer update -vvv 然后"composer dump-autoload"

93. 安装laravel, 需要修改composer配置文件为composer config -g repo.packagist composer https://packagist.phpcomposer.com。然后修改laravel的composer.json文件
加上"repositories": {
        "packagist": {
            "type": "composer",
            "url": "https://packagist. phpcomposer. com"
        }
    }，记得配置. env文件不然会报错。

94. 新建的项目php artisna make:auth 会自动写好注册页面，然后php artisan migrate自动创建users表和passwor_resets表

95. php artisan make:model Models/Admin -m 加上-m会生成对应迁移文件，*_create_admins_table。

96. php artisan make:seeder AdminsTableSeeder创建种子文件，在database/seeds下面

97. 数据模型工场,database/factories/ModelFactory. php

98. mt_rand 效率更快点

99. SideBarEnhancements sublime text3 文件删除，刷新插件

100. export NODE_ENV=develop && gulp编译anuglarjs+gulp 文件

101. gulp中. pipe(concat('all. js'))//合并后的文件名

102. postman post请求中参数应该在body里面。不是在headers里面

103. \Config::get('reoder');读取配置里面的东西

104. php artisan vendor:publish --provider="Maatwebsite\Excel\ExcelServiceProvider" 在config 目录下生成excel. php配置文件。修改to_ascii = > true 就可以读取中文。

105. echo Artisan::output(); laravel 获取Artisan::call的执行结果, 或者
`    
    use Symfony\Component\Console\Output\BufferedOutput;
    \Artisan::call('tmp:export:classroomdetails', ['package_id' => implode(',', $package_id)], $output);
    return $output->fetch(); 
`
106.  
`   
	\Excel::selectSheets("Sheet1")->load($path, function ($results) {
        $num = 0;
        $count = $results->count();
        $this->info('总共:'. $count. '条数据,');
        foreach ($results->get()->chunk(100) as $row) {
            $this->send($row, $num);
        });
    });
`
107. 
`
	\Excel::filter('chunk')->selectSheetsByIndex(0)->load($path)->chunk(100, function($results){
	    
	});
`

108. redis 停止,启动,重启命令
`
	/etc/init. d/redis-server stop
    /etc/init. d/redis-server start
    /etc/init. d/redis-server restart
`

109. redis和mongodb的视图管理工具不能连接，虚拟机里面的时候，修改/etc目录下的配置文件。
修改bind_ip = 0. 0. 0. 0，然后重启下服务即可。

110. SELECT openclass_id,no FROM `schedules` WHERE openclass_id >0 GROUP BY openclass_id,no HAVING COUNT(no)>6获得班课某个章节人数大于6 的班课,根据openclass_id,no分组

111. mysql 命令导入sql文件, mysql -u root -p testiZ284vyq3w4Z  –default-character-set  = utf8  db < C:lewaijiao.sql

112. cmd进入redis目录 输入redis-server.exe  redis.conf 启动, cmd进入mongodb\bin\目录输入mongod --dbpath D:\Mongodb\data 启动

113. git提交步骤(1). git status 查看状态(2).git add 文件 增加文件;(3). git commit -m " 注释.增加了评论……" ;(4). git push有冲突,用git pull解决冲突
git branch 查看分支 

114. 新建项目需要 1.git clone 网址 2.拉取core分支代码3.composer  dumpautoload4.配置env文件5.composer update 

115. js字符串转成日期 
 `   
    var str ='2012-08-12 23:13:15';
    str = str. replace(/-/g,"/");
    var date = new Date(str);
    console. log(date);
`

116. sudo vim 编辑文件,用/+ 字符串进行查找字符串的位置,已经用/找到了一个词,那么n(下一个),N(上一个)再找这个词

117. use Illuminate\Database\Eloquent\SoftDeletes; $table->softDeletes(); laravel软删除。

118. sudo cnpm install supervisor -g， node. js安装Node Supervisor原本是用于服务器上Node. js应用崩溃的时候，自动重新启动。当然它也可以监控你的项目的js文件变化，自动重启来方便调试。使用方法supervisor app. js 即可监控js. 

119. laravel excel 获取header
`
    public function getHeaders(SplFileInfo $file)
    {
        return ((((\Excel::load($file))->all())->first())->keys())->toArray(); // I wrap in 1 line just for fun :)
    }
`

120. [正向代理」代理的对象是客户端,[反向代理」代理的对象是服务端
    文章链接:https://foofish.net/proxy-and-reverse-proxy.html

121. php artisan make:migration add_main_tag_colunmn_to_lechat_teachers --table=teachers

    php artisan make:migration change_link_to_colunmn_to_client_ad_table --table=client_ads

    php artisan make:migration rename_user_id_colunmn_to_lechat_wallets_table --table=wallets

    php artisan make:migration remove_lead_id_colunmn_to_lechat_checkin_member_table --table=checkin_members

122. mysql 命令导入mysql文件。mysql -u root -p testiZ284vyq3w4Z  –default-character-set  = utf8  db < C:lewaijiao. sql

123. source C:\lewaijiao. sql

124. 字符串相加：(1). 如果字符串是以数字开头的,则只取数字部分,数字后面的字符串去除也包括这种(ss123),
    (2). 如果字符串是以字母开头的,则直接转换整型数字0。然后进行相加

125. strcmp(),比较字符串,当字符串之间第一位不相等时候, 结束为字符串第一位之间的差值。如果相等,则后面多几个字符串就是多一个1
example:
`    
    strcmp('a', 'dsdfsdf');//-3
    strcmp('ab', 'dcdfsdf');//-3
    strcmp('as', 'asdfsdf');//-5
`

126. mysql_connect()已经被抛弃。mysqli只能连接mysql,PDO应用12种不同数据库

127. 数组分为索引数组(带有数字索引的数组),关联数组(键值对数组)和多维数组(包含一个或多个数组的数组)

128. 安装Doc​Blockr插件，然后在sublime text3 输入/**，按enter就可以增加多行自动注释

129. 1. git mv --force commentProductController.php CommentProductController.php 	windows环境下在git中修改文件的大小写。
	2. git mv -force myFile.txt MyFile.txt或者git mv -f myFile MyFile,它会自动add,直接commit 
	3. 或者配置git文件，Add ignorecase = false to [core] in .git/config;

130. 接口和抽象类的区别：
		1. 接口（interface 接口名字）是规范，继承的子类必须实现接口的所有方法。否则会报错（接口类就是一个类的领导者，指明方向，子类必须完成它指定方法），接口用implements 继承
		2. 抽象类(abstract test)的继承用extends,必须完成抽象类的抽象方法，但不一定需要实现public的方法，
		3. 接口没有构造函数，抽象类可以有构造函数
		4. 一个类可以同时实现多个接口，但一个类只能继承于一个抽象类
		5. 接口中不可以声明成员变量（包括类静态变量），但是可以声明类常量。抽象类中可以声明各种类型成员变量，实现数据的封装
		
131. 1. 值拷贝是深拷贝，而指针拷贝是浅拷贝。

	 2. 浅复制(通过一个原型实例,这里暂称为老对象。克隆所得到的对象--这里暂时称为新对象)又称为浅拷贝，深复制又称为深拷贝

132.  div居中 style= "width:50%;margin:0 auto;"
133.  HTTP的8钟请求
	1.  get（相当于资源的查）：一般用于获取/查询 资源信息，如果请求具有幂等性就可以使用GET，所谓幂等是指多个请求返回相同的结果。get提交的数据最多是1024字节，HTTP协议没有对传输的数据大小进行限制，HTTP协议规范也没有对URL长度进行限制。特定浏览器和服务器对URL长度有限制，理论上没有长度限制，其限制取决于操作系统的支持，web浏览器和web服务器的限制
	2.  post（相当于资源的增）:，创建操作可以使用POST，也可以使用PUT，区别在于POST 是作用在一个集合资源之上的（/articles），在每次更新提交相同的内容，最终的结果不一致的时候，用POST，举个很常见的例子，一个接口的功能是将当前余额减一个值，每次提交指定该值为100，接口如下/amount/deduction调用一次，你的余额-100，调用两次，余额-200。这个时候就用POST
	3.  put（相当于资源的改):,操作是幂等的。而PUT操作是作用在一个具体资源之上的（/articles/123）。
	4.  delete（相当于资源的删):,用于删除请求URL上的某个资源，为了安全，用post请求，设置input type='hidden' value='delete'。
	5.  head: 方法跟GET方法相同，1. 只不过服务器响应时不会返回消息体,HEAD节省资源，用于检查超链接的有效性。检查网页是否被修改，2. 类似于GET, 但是不返回body信息,用于检查对象是否存在，以及得到对象的元数据
	6.  options:用途：1. 获取服务器支持的HTTP请求方法；也是黑客经常使用的方法。2. 用来检查服务器的性能。例如：AJAX进行跨域请求时的预检，需要向另外一个域名的资源发送一个HTTP OPTIONS请求头，用以判断实际发送的请求是否安全
	7.  trace: 用于远程诊断服务器
	8.  connect:用于代理进行传输，如使用SSL,翻墙代理等

134. CORS(跨域资源共享),以前要实现跨域访问，可以通过JSONP、Flash或者服务器中转的方式来实现，但是现在我们有了CORS.
	1. JSONP只能实现GET请求，而CORS支持所有类型的HTTP请求。
    2. 使用CORS，开发者可以使用普通的XMLHttpRequest发起请求和获得数据，比起JSONP有更好的错误处理。
    3. JSONP主要被老的浏览器支持，它们往往不支持CORS，而绝大多数现代浏览器都已经支持了CORS（这部分会在后文浏览器支持部分介绍）
135. fetch_array(MYSQLI_NUM) 从结果集中取得一行作为索引数组，即$arr[0]
	fetch_array(MYSQLI_ASSOC),从结果集中取得一行作为关联数组,即$arr['name']

136. mysql_fetch_row和mysql_fetch_array的区别是第一个函数返回的数组是只包含值，只能通过数组下标来读取数据.msyql_fetch_array返回的数组可以包含为索引数组和关联数组，或者两者兼有。
137. mysqli是mysql扩展的升级版，mysql扩展已经被弃用。

138. mysql_fetch_array是面向过程方式操作数据库，fetch_array是面向对象方式操作数据库！建议使用面向对象

139. php是单继承语言，没有trait之前无法同时从两个基类继承属性或方法。Trait不能直接实例化，通过use TestTrait进行使用。[blog 链接](https://segmentfault.com/a/1190000002970128)
140. RPC 远程调用 [csdn链接](http://blog.csdn.net/diandianxiyu_geek/article/details/52294201), [segmentfault链接](https://segmentfault.com/q/1010000000318121)

141. mysql主从数据库master db（主数据库,主服务器）一般负责数据的写入，和slave db(从数据库，从服务器)一般负责读取数据，实现了读写分离。 [详情](http://www.cnblogs.com/jirglt/p/3549047.html)，master将改变记录到二进制日志中（这些记录叫做二进制日志事件)，slave将master的二进制日志事件拷贝到它的中继日志， slave重做中继日志中的事件，将改变反映它自己的数据

142. MVC是一种C/S或者B/S软件工程的组织方式
143. 数据库的三范式(设计表的时候，大部分都需要满足数据库的三范式)
	1. 第一范式：确保每列的原子性（如果每列都是不可再分的最小数据单元，则满足第一范式）
	2. 第二范式： 在第一范式的基础上，更近一层，目标是确保表中的每列都和主键相关，如果一个关系满足第一范式，并且除了主键以外的其他列。都依赖于该主键，则满足第二范式
	3. 第三范式: 在第二范式的基础上，目标是确保每列都和主键列直接关系，而不是间接关系

144. NoSql: 非关系型数据库

145. redis是可远程的，（1）. 有客户端和服务端两个部分、通过redis自定义传输进行连接的，(2). redis和mongodb基于内存的，比mysql存到到硬盘快很多，(3). 非关系型数据库。

146. redis应用场景: 1. 缓存 2. 队列，push(在队列尾部增加元素),pop(弹出队列的最后一个元素)的操作保证了原子性的实现  3. 数据储存
 
147. PHP_EOL相当于换行符。使用PHP_EOL可以提高代码的源代码级可移植性。在unix系列用\n,在windows系列用\r\n,在mac用\r,php中可以用PHP_EOL来替代，对于浏览器来说，文本换行要使用<br>标记，通常文件里文本的换行，只对文本有效，对浏览器没有作用。(2).可以查看源代码，看到PHP_EOL是否有效。

148. redis 数据类型
	1. string类型。key=>value(string/int/float)
	int类型自增set test 1, get test(结果是1)，incr test（自增），get test (结果2)
	decrby test 2 (decrby 变量名称 数字)，减法，test变量-2
	2. list类型是一个有序列表。左边是key,右边是value.其中value中上面是左，下面是右。lpush从左边进行插入，rpush从右边进行插入，rpop从右边进行弹出，lpop进行弹出。 **list类型不要去字符串类型唯一，即可以重复** llen list 查看长度
	3. set类型， 左边是key,右边是value,**每个元素的值都不一样，不可以重复**，scard查看长度，sismember myset a 判断a是否已存在。返回1为存在，0位不存在。srem myset a 删除a元素
	4. hash类型，左边key,右边自上而下，key1/value(string/int/float), key2/value(string/int/float)等。设置值:hset hash1 key1 12, 获取多个值hmset hash1 key1 key2
	5. sort set类型，score，查看排名: zrank zset value1 查看zset表中value1中的排名，排名从0开始
	
149. Nginx和PHP-FPM的启动/重启脚本[链接](https://www.lovelucy.info/nginx-phpfpm-init-script.html)。/etc/init.d/php-fpm restart