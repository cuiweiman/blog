![离线记事本](./images/1536235549860.png)

这是一个笔记应用,不需要联网,也不需要数据库,可以直接把数据储存在本地.方便易用! \^_^

``` html?linenums
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>记事本</title>
    <script src="http://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
    <style>
        *{ margin: 0; padding: 0; box-sizing: border-box; font-family: 'Microsoft Yahei'; font-size: 14px; box-sizing: border-box;}
        h1{text-align: center; font-size: 26px; line-height: 60px;color: #ff8140;}
        .main{margin: 10px auto;width: 1000px; overflow: hidden; height: 500px;padding: 1%;border: 1px solid #ddd; border-radius: 5px;}
        input{ width: 92%; padding: 5px; outline: none;border: 1px solid #eee;}
        textarea{ width: 100%; overflow: hidden; padding: 5px; outline: none; resize:none; }
        textarea:focus,input:focus { border-color: #f77c3d; }
        .write{padding: 10px; border-radius: 3px; background: #eee; overflow: hidden; float: left;width: 48%;}
        .send{ background: #ff8140; border: 1px solid #f77c3d; color: #fff; box-shadow: 0px 1px 2px rgba(0, 0, 0, 0.25);
            width: 80px; height: 30px; text-align: center; line-height: 30px; border-radius: 3px; float: right; cursor: pointer; }
        .list{padding: 10px; float: left;width: 50%;}
        .item{padding: 10px;border: 1px solid #eee;border-radius: 3px;margin-bottom: 10px;}
        .item .content{padding: 20px 10px;word-break:break-all;background: #fff; display: none;}
        .title{padding-bottom: 5px;border-bottom: 1px solid #eee;cursor: pointer;}
        .title span{color: #999; font-size: 12px;float: right;}
        #noteList{overflow-y: scroll;height: 350px;box-shadow: 0 0 3px #ddd;}
    </style>
</head>
<body>
<h1>记事本</h1>
<div class="main">
    <div class="write" id="write">
        标题: <input id="title" type="text" placeholder="请输入标题"><br><br>
        分类: <select id="type">
                <option value="默认" selected>-请选择-</option>
                <option value="美食">美食</option>
                <option value="代码">代码</option>
                <option value="生活">生活</option>
            </select>
        <br><br>
        <textarea name="" id="cont" cols="30" rows="10" placeholder="今天想说点啥..."></textarea>
        <div class="send" id="add">添加</div>
    </div>
    <div class="list" id="list">
        标题: <input id="title1" type="text" placeholder="查询标题"><br><br>
        分类: <select id="type1">
                <option value="默认" selected>-请选择-</option>
                <option value="美食">美食</option>
                <option value="代码">代码</option>
                <option value="生活">生活</option>
            </select>
        <div id="search" class="send">查询</div><br><br>
        <div id="noteList"></div>
    </div>
</div>
<script type="template" id="temp">
    <div class="item">
        <div class="title">@title<span>@type: @date</span></div>
        <div class="content">@cont</div>
    </div>
</script>
<script>
$(function(){
    var init = {title:'这是标题',
        date:new Date().toLocaleString(),
        type:'示例',
        cont:'这是一个笔记应用,不需要联网,也不需要数据库,可以直接把数据储存在本地.方便易用!^_^'};
    function show(notes){
        var temp = $('#temp').html();
        var tempStr= '';
        //如果不是数组,变成数组
        if(!Array.isArray(notes)){
            notes = [notes];
        }
        for(var i=notes.length-1;i>-1;i--){
            var note = notes[i];
            tempStr += temp.replace('@title',note.title)
                    .replace('@date',note.date)
                    .replace('@type',note.type)
                    .replace('@cont',note.cont);
        }
        $('#noteList').html(tempStr);
    }

    //读取所有数据
    function showList(){
        var notes = localStorage.getItem('notes');
        if(!notes){
            show(init);
        }else{
            notes = JSON.parse(notes);
            show(notes);
        }
        //第一个展开
        $('#noteList .item:first').find('.title').trigger('click');
    }

    $('#add').click(function(){
        var title = $('#title').val();
        var cont = $('#cont').val();
        var type = $('#type').val();
        if(title == ''){
            alert('标题不能为空!');
            return;
        }
        var data = {title:title,cont:cont,type:type,date:new Date().toLocaleString()};
        var notes = localStorage.getItem('notes');
        if(!notes){
            notes = [];
        }else{
            notes = JSON.parse(notes);
        }
        notes.push(data);
        localStorage.setItem('notes',JSON.stringify(notes));
        showList();
    });

    $('#search').click(function(){
        var title = $('#title1').val();
        var type = $('#type1').val();
        var notes = localStorage.getItem('notes');
        if(!notes){
            alert('没有本地笔记数据!');
            return;
        }else{
            notes = JSON.parse(notes);
        }
        var note = [];
        for(var i=0;i<notes.length;i++){
            //notes[i] json对象
            var flag = false;
            if(!title){
                flag = notes[i].type==type;
            }else if(!type){
                flag = notes[i].title.indexOf(title)>-1;
            }else{
                flag = notes[i].title.indexOf(title)>-1 && notes[i].type==type;
            }
            if(flag){
                note.push(notes[i]);
            }
        }
        if(note.length == 0){
            alert('抱歉~没有对应的笔记!');
        }else{
            show(note);
        }
    });

    $('body').on('click','#noteList .title', function(){
        $(this).next().slideToggle();
    });

    showList();
})
</script>
</body>
</html>
```