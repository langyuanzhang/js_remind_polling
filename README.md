# js_remind_polling
js订单消息提醒（轮询方式）



```html
<!-- 订单声音 -->
<audio id="mp3" src="__common__/mp3/wanan.mp3"> </audio>
```

```js
<!-- 订单 -->
<script>
    var mp3 = $("#mp3")[0];
    
    //初次进来时未处理订单数
    $.ajax({
      url:"{:url('admin/index/promptno')}",
      success:function (data) {
        document.getElementById("change").innerText=parseInt(data);
        }
    });

    setInterval(function () {
    $.ajax({
        url:"{:url('admin/index/prompt')}",
        success:function (data) {
        console.log(data)
        if (data) {
            // 播放提醒
            mp3.play();
            //更新未读订单数
            document.getElementById("change").innerText=parseInt(data);

            }
        }
        })
    },5000)
</script>
```

```php
//订单提醒
public function prompt()
{
    $post = $this->request->post();

    // 由于Ajax三秒钟才执行一次，所以新数据的插入时间要晚于查询的的请求时间（当前时间）三秒钟
    $time = time() - 5;

    //查询订单表新订单总条数 未处理1
    $res=Db::name('order')->where('prompt','1')->order('id desc')->select();

    if($res){
        foreach ($res as $k => $v) {
            //该订单是创建时间戳
            $catime = strtotime($v['created_at']);

            if($catime>$time){
                $cot=Db::name('order')->where('prompt','1')->count();
                return $cot;
            }
        }
    }

}

//订单提醒
public function promptno()
{
    $cot=Db::name('order')->where('prompt','eq','1')->count();
    return $cot;
}
```
