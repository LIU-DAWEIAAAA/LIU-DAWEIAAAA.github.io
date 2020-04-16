---
title: QQ音乐网页版(个人练习)
date: 2020-04-10 17:31:01
valineenbale: true 
declare: true
tags: [JS,jQuery,学习记录]
---

# QQ音乐网页版(个人练习)

在线预览地址：https://liu-daweiaaaa.github.io/QQMUSIC-demo/

## 参考资料
1.b站 BV17W41137jn

2.github https://github.com/Soul-Silver/QQMusic-demo


## 练习目的

1.综合JS、jQuery进行练手。

2.对JS、jquery基础进行加强。

3.尝试简单的网页综合项目。
<!-- more -->

## 练习注意点

1.如果没有服务器的话需要添加：--args --disable-web-security --user-data-dir 这个命令来实现跨域。
推荐放在服务器上运行

2.CSS设置点击变化图标的步骤需要使用不同位置的图像

  /* 单独设置图标 每个都有不同的位置 */

    .content_toolbar span:nth-child(1) i{
	background-position: -60px -20px;
    }

3.注意CSS的命名，方便查找。

4.CSS样式中 !important代表优先级无限高。

5.学会运用事件委托方法delegate。

6.了解并使用了回调函数。

7.建立了JS的对象与方法。

8.进度条工具是点击坐标与元素左侧坐标相减。

9.在拖拽完进度条后需要结束mousemove、mousedown、click事件，不然小圆点会一直随动鼠标。

10.拖动进度条事件需要包含在点击进度条事件当中，鼠标抬起事件要包含在拖拽进度条事件当中。

11.对歌词的提取需要用到ajax。

12.歌词的时间提取需要用到正则表达式。

## 项目对网上实例的改进地方

1.添加了纯净模式下的海报，歌曲信息。

2.添加了键盘操作暂停、快进快退的方法。

3.对进度条进行了优化，修复了进度条点击暂停之后再次继续播放失效的bug。

4.完善了CSS样式，对进度条工具进行了修改。

5.对歌曲列表进行了重新设计布局，完善了菜单栏的样式。

## 项目不足之处

1.进度条工具会出现鼠标跟随的bug。

2.当点击暂停时歌词高亮消失，点击继续播放时会重头开始遍历歌词。

3.当拖动进度条时，向前拖动进度条歌词不会自动刷新，只有向后拖动才会跳转到当前歌词。

4.删除歌曲有未知bug，有时候不会正常删除。

5.喜欢按钮仅仅完成部分，未能全部实现功能。

6.手机端未做适配，浏览会错版。


## 主要代码

### HTML
```HTML

<!doctype html>
<html>
<head>
	<meta charset="utf-8">
	<title class="MusicTitle">QQ音乐</title>
	<!--qq音乐logo-->
	<link rel="icon" href="images/bitbug_favicon.ico">
	<!--先导入其他框架的css 再导入自己的-->
	<link rel="stylesheet" href="CSS/jquery.mCustomScrollbar.css">
	<link rel="stylesheet" href="CSS/index.css" >
	
	<!--进度条框架基于jquery 所以先导jquery 再导入框架 再导入自己的js-->
	<!--<script src="JS/jquery-1.12.4.js"></script>-->
	<script src="JS/jquery-3.4.1.js"></script>
	<script src="JS/jquery.mCustomScrollbar.concat.min.js"></script>
	
	<!--引入player要在自己的index之前引入 因为在index之中用了她-->
	<script src="JS/player.js"></script>
	<script src="JS/progress.js"></script>
	<script src="JS/lyric.js"></script>
	<script src="JS/index.js"></script>
	
	
</head>

<body>
	
	<!--头部div-->
	<div class="header">
		<div class="header_in">
			<h1 class="logo"><a href="#"></a></h1>
			<ul class="register">
				<li>登录</li>
				<li>设置</li>
			</ul>
		</div>
	</div>
	
	<!--内容div-->
	<div class="content">
		<div class="content_in">
			<div class="content_left">
				<div class="content_toolbar">
					<span class="favorite_music"><i></i>收藏</span>
					<span><i></i>添加到</span>
					<span><i></i>下载</span>
					<span class="delete_music"><i></i>删除</span>
					<span class="clear_musicList"><i></i>清空列表</span>
				</div>
				
				<!--/*测试菜单标题栏*/-->
				<div class="content_list_title">
					<ul>
						<!--标题标签-->
						<li class="list_title">
							<div class="list_check"><i></i></div>
							<div class="list_number"></div>
							<div class="list_name">歌曲</div>
							<div class="list_singer">歌手</div>
							<div class="list_time">时长</div>
						</li>
					</ul>
				</div>
				
				<div class="content_list" data-mcs-theme="minimal-dark">
					
					<ul>
						
						<!--标题标签-->
						<!--
						<li class="list_title">
							<div class="list_check"><i></i></div>
							<div class="list_number"></div>
							<div class="list_name">歌曲</div>
							<div class="list_singer">歌手</div>
							<div class="list_time">时长</div>
						</li>-->
					</ul>
					
						<!--实例-->
						<!--<li class="list_music">
							<div class="list_check"><i></i></div>
							<div class="list_number">test1</div>
							<div class="list_name">歌曲LDW
								<div class="list_menu">
									<a href="javascript::" title="播放"></a>
									<a href="javascript::" title="添加"></a>
									<a href="javascript::" title="下载"></a>
									<a href="javascript::" title="分享"></a>
								</div>
							</div>
							<div class="list_singer">歌手LDW</div>
							<div class="list_time">
								<span>05:20</span>
								<a href="javascript::" title="删除" class="list_menu_del"></a>
							</div>
						</li>-->
						
						
					
				</div>
			</div>
			<div class="content_right">
				<div class="song_info">
					<!--a做背景-->
					<a href="javascript:;" class="song_info_pic">
						<!--img做歌曲封面-->
						<img src="images/LDW.jpg" alt="">
					</a>
					
					<!--设置歌曲信息-->
					<div class="song_info_name">歌曲名称:
						<a href="javascript:;">名称LDW</a>
					</div>
					
					<div class="song_info_singer">歌手名:
						<a href="javascript:;">名LDW</a>
					</div>
					
					<div class="song_info_album">专辑名称:
						<a href="javascript:;">专辑LDW</a>
					</div>
					
				</div>
				
				<div class="song_lyric_container">
					<ul class="song_lyric"></ul>
				</div>
				
			</div>
		</div>
		
		<!--纯净模式的div-->
			<div class="content_only_in">
				<div class="content_only_left">
					<div class="song_info">
						<!--a做背景-->
						<a href="javascript:;" class="song_info_pic">
							<!--img做歌曲封面-->
							<img src="images/LDW.jpg" alt="">
						</a>

						<!--设置歌曲信息-->
						<div class="song_info_name">歌曲名称:
							<a href="javascript:;">名称LDW</a>
						</div>

						<div class="song_info_singer">歌手名:
							<a href="javascript:;">名LDW</a>
						</div>

						<div class="song_info_album">专辑名称:
							<a href="javascript:;">专辑LDW</a>
						</div>

					</div>
				</div>

				<div class="content_only_right">
					<div class="only_song_lyric">
						<ul class="song_lyric"></ul>
					</div>
				</div>
			</div>
		
	
	</div>
	
			
		
	
	
	<!--脚部div-->
	<div class="footer">
		<div class="footer_in">
			<!--三个标签：上一首 暂停 下一首-->
			<a href="javascript:;" class="music_pre"></a>
			<a href="javascript:;" class="music_play"></a>
			<a href="javascript:;" class="music_next"></a>
			
			<!--信息栏+进度条 -->
			<div class="music_progress_info">
				<div class="music_progress_top">
					<span class="music_progress_name">QQ音乐/LDW</span>
					<span class="music_progress_time">00:00/05:20</span>
				</div>
				
				<!--进度条设置：淡白色背景 纯白色前景 白色圆点-->
				<div class="music_progress_bar">
					<div class="music_progress_line">
						<div class="music_progress_dot"></div>
					</div>
				</div>
			</div>
			
			<!--五个标签-->
			<a href="javascript:;" class="music_mode" id="MODE"></a>
			<a href="javascript:;" class="music_fav"></a>
			<a href="javascript:;" class="music_down"></a>
			<a href="javascript:;" class="music_comment"></a>
			<a href="javascript:;" class="music_only"></a>
			
			<!--音量键-->
			<div class="music_voice_info">
				<a href="javascript:;" class="music_voice_icon"></a>
				
				<!--进度条设置：淡白色背景 纯白色前景 白色圆点-->
				<div class="music_voice_bar">
					<div class="music_voice_line">
						<div class="music_voice_dot"></div>
					</div>
				</div>
			</div>
			
		</div>
	</div>
	
	<!--背景div-->
	<div class="mask_bg">
		<div class="mask_bg_in"></div>
	</div>
	
	<div class="mask">
		<div class="mask_in"></div>
	</div>
	
	<!--开始设置歌曲播放-->
	<audio src=""></audio>
	
	
</body>
</html>


```

### index.js
```JavaScript
//index.js
$(function(){
	
	
	//利用外部模块自定义滚动条
	//0.给自己的类定义
	$(".content_list").mCustomScrollbar();
	
	//设置变量
	var $audio = $("audio");
	var player = new Player($audio);
	//歌曲进度条
	var progress;
	//声音进度条
	var voiceProgress
	//歌词信息
	var lyric;
	
	
	//1.首先加载歌曲列表
	getPlayerList();
	function getPlayerList(){
		$.ajax({
			//注意！！！！！！！！！！！！
			//个人练习时候可以使用：chrome 浏览器版本在49以后都需要使用 open -a Google\ Chrome: --args --disable-web-security --user-data-dir 这个命令来实现跨域。
			//企业开发不推荐，请去学习服务器的“跨域”知识
			
			url:"./source/musiclist.json",
			dataType:"json",
			success:function(data){
				player.musicList = data;
				
				//遍历获取到的数据，创造每一首歌曲
				var $musicList = $(".content_list ul");
				$.each(data,function(index,ele){
					var $item = createMusicItem(index,ele);
					$musicList.append($item);
				});
				
				//加载完歌曲列表就要初始化歌曲信息
				//默认加载第1首歌曲信息
				initMusicInfo(data[0]);
				
				//加载歌词信息
				initMusicLyric(data[0]);
				
			},
			error:function(e){
				//打印错误内容
				console.log(e);
				console.log("2");
			}
		});	
	}
	
	//2.初始化歌曲信息
	function initMusicInfo(music){
		//1.获取对应元素
		//右侧界面歌曲信息
		var $musicImage = $(".song_info_pic img");
		var $musicName = $(".song_info_name a");
		var $musicSinger = $(".song_info_singer a");
		var $musicAlbum = $(".song_info_album a");
		
		//底部进度条歌曲信息
		var $musicProgressName = $(".music_progress_name");
		var $musicProgressTime = $(".music_progress_time");
		
		//背景图片信息
		var $musicBg = $(".mask_bg");
		
		//顶部title的播放歌名
		var $musicTitle = $(".MusicTitle");
		
		//2.给对应元素赋值
		$musicImage.attr("src",music.cover);
		$musicName.text(music.name);
		$musicSinger.text(music.singer);
		$musicAlbum.text(music.album);
		$musicProgressName.text(music.name+" / "+music.singer);
		$musicProgressTime.text("00:00 / "+music.time);
		$musicBg.css("background","url( '"+music.cover+"')");
		$musicTitle.text("QQ音乐："+music.name+" / "+music.singer);
		
	}
	
	//3.初始化歌词信息
	
	function initMusicLyric(music){
		lyric = new Lyric(music.link_lrc); 
		var $lyricContainer = $(".song_lyric");
		
		//清空上一首音乐歌词
		$lyricContainer.html("");
		
		lyric.loadLyric(function(){
			//创建歌词列表(遍历创建)
			$.each(lyric.lyrics,function(index,ele){
				var $item = $("<li>"+ele+"</li>");
				$lyricContainer.append($item);
				
			});
			
		});
		
	}
	
	
	//3.初始化进度条
		initProgress();
		function initProgress(){
			//进度条变量
			var $progressBar = $(".music_progress_bar")
			var $progressLine = $(".music_progress_line")
			var $progressDot = $(".music_progress_dot")
			
			console.log("进度条初始化");
			
			
			progress = Progress($progressBar,$progressLine,$progressDot);
			//点击进度条事件
			progress.progressClick(function(value){

				player.musicSeekTo(value);
				console.log("1");
				
			});
			
			
			//拖拽进度条事件
			progress.progressMove(function(value){
				
				player.musicSeekTo(value);
				//初始歌词
				
				console.log("2");
				
			});
			
			
			
			//--------------------------设置音量进度条
			//音量进度条变量
			var $voiceBar = $(".music_voice_bar");
			var $voiceLine = $(".music_voice_line");
			var $voiceDot = $(".music_voice_dot");

			voiceProgress = Progress($voiceBar,$voiceLine,$voiceDot);
			//点击声音进度条事件
			voiceProgress.progressClick(function(value){

				player.musicVoiceSeekTo(value);
			});

			//拖拽声音进度条事件
			voiceProgress.progressMove(function(value){
				player.musicVoiceSeekTo(value);
				if(value >0 ){
					//增加事件 拖拽进度条使静音失效
					$(".music_voice_icon").removeClass("music_voice_icon2");
				}
			});
			
		}
	
	
	//将监听事件都整合到一个功能里面
	//2.初始化事件监听
	initEvents();
	function initEvents(){
		
		//1.监听歌曲的移入移出事件
		//动态创建的列表需要事件委托
		$(".content_list").delegate(".list_music","mouseenter",function(){
			//显示子菜单
			//一般找元素用find
			$(this).find(".list_menu").stop().fadeIn(100);
			$(this).find(".list_time a").stop().fadeIn(100);
			//隐藏时长
			$(this).find(".list_time span").stop().fadeOut(100);

		});

		$(".content_list").delegate(".list_music","mouseleave",function(){
			//隐藏子菜单
			$(this).find(".list_menu").stop().fadeOut(100);
			$(this).find(".list_time a").stop().fadeOut(100);
			//显示时长
			$(this).find(".list_time span").stop().fadeIn(100);

		});

		//注释直接添加的代码
		/*$(".list_music").hover(function(){
			//显示子菜单
			//一般找元素用find
			$(this).find(".list_menu").stop().fadeIn(100);
			$(this).find(".list_time a").stop().fadeIn(100);
			//隐藏时长
			$(this).find(".list_time span").stop().fadeOut(100);
		},function(){
			//隐藏子菜单
			$(this).find(".list_menu").stop().fadeOut(100);
			$(this).find(".list_time a").stop().fadeOut(100);
			//显示时长
			$(this).find(".list_time span").stop().fadeIn(100);
		});
		*/

		//2.监听复选框的点击事件
		//动态添加：事件委托
		$(".content_list").delegate(".list_check","click",function(){
			$(this).toggleClass("list_checked");
		});
		
		$(".content_list_title .list_title").delegate(".list_check","click",function(){
			$(this).toggleClass("list_checked");
		});

		/*//测试直接添加
		$(".list_check").click(function(){
			$(this).toggleClass("list_checked");

		});*/

		

		//3.添加子菜单播放按钮的监听
		var $musicPlay = $(".music_play");
		$(".content_list").delegate(".list_menu_play","click",function(){
			//优化代码
			var $item = $(this).parents(".list_music");
			
			/*//拿到原生的存放歌曲信息的li 打印出来！
			console.log($item.get(0).index);
			console.log($item.get(0).music);*/
			
			//3.1切换播放的图标
			$(this).toggleClass("list_menu_play2");
			//3.2复原其他播放的图标
			$item.siblings().find(".list_menu_play").removeClass("list_menu_play2");

			//3.3同步底部的播放按钮
			//判断当前点击的按钮是否包含所选类名
			if($(this).attr("class").indexOf("list_menu_play2") != -1){
				//包含
				//当前子菜单播放按钮是播放的状态
				$musicPlay.addClass("music_play2");
				//让文字高亮
				$item.find("div").css("color","#fff");
				$item.siblings().find("div").css("color","rgba(255,255,255,0.5)");

			}else{
				//当前子菜单按钮不是播放状态
				$musicPlay.removeClass("music_play2");
				//让文字不高亮
				$item.find("div").css("color","rgba(255,255,255,0.5)");

			}

			//3.4切换序号的状态
			$(this).parents(".list_music").find(".list_number").toggleClass("list_number2");
			$(this).parents(".list_music").siblings().find(".list_number").removeClass("list_number2");
			
			//3.5播放音乐
			player.playMusic($item.get(0).index,$item.get(0).music);
			
			//3.6切换歌曲信息(见第二步)
			initMusicInfo($item.get(0).music);
			
			//3.7切换歌词信息
			initMusicLyric($item.get(0).music);
			
					
		});
		
		//4.监听底部控制区域播放按钮的点击
		$musicPlay.click(function(){
			//判断有没有播放过音乐
			if(player.currentIndex == -1){
				//没有播放过音乐
				$(".list_music").eq(0).find(".list_menu_play").trigger("click");
				
			}else{
				//已经播放过音乐
				$(".list_music").eq(player.currentIndex).find(".list_menu_play").trigger("click");
			}
			
		});
		
		//一般情况:下一首就是currentIndex+1 上一首就是currentIndex-1
		//特殊情况：第一首的上一首就是最后一首  最后一首的下一首就是第一首
		//5.监听底部控制区域上一首按钮的点击
		$(".music_pre").click(function(){
			$(".list_music").eq(player.preIndex()).find(".list_menu_play").trigger("click");
			
		});
		
		//6.监听底部控制区域下一首按钮的点击
		$(".music_next").click(function(){
			$(".list_music").eq(player.nextIndex()).find(".list_menu_play").trigger("click");
			
		});
		
		//7监听删除按钮的点击
		$(".content_list").delegate(".list_menu_del","click",function(){
			//删除一个是从界面上删除  后台的music[]也要删除
			//找到被点击的音乐
			var $item = $(this).parents(".list_music");
			
			//还要判断删除的音乐是否为当前播放的 如果当前正在播放应该立即停止 并且自动切换播放到下一首
			if($item.get(0).index == player.currentIndex){
					//当前删的是播放的
					//等于点击一下：下一首
					$(".music_next").trigger("click");
				
			}
			
			//设置删除的函数
			$item.remove();
			player.changeMusic($item.get(0).index);
			
			
			//进行重新排序
			//1.歌曲的序号需要重新排序   2.后面原生的li里面的索引也要重新排序
			$(".list_music").each(function(index,ele){
				//当前变量的li的索引 改成最新的index
				ele.index = index;
				//把对应的number改一改
				$(ele).find(".list_number").text(index+1);
				
			});
		});
		
		musicTimeUD();
		function musicTimeUD(){
		//8.监听播放的进度
		player.musicTimeUpdate(function(currentTime,duration,timeStr){
			
			//1.同步时间
			$(".music_progress_time").text(timeStr);
			
			//2.同步进度条
			//计算播放的比例
			var value = currentTime/duration * 100;
			//在进度条类里面新增一个方法
			
			progress.setProgress(value);//同步进度条的位置！！！！！！！-----所以暂停时也要同步一下
			
			//实现歌词的同步
			var index = lyric.currentIndex(currentTime);
			//找到歌词容器，将当前索引歌词标位高亮
			var $item = $(".song_lyric li").eq(index);
			$item.addClass("cur");
			$item.siblings().removeClass("cur");
			
			//设置歌词滚动
			//滚动ul
			if(index<= 2) return; //此时ul会向上滚 但是是整个向上 所以需要去结构中增加一个范围div
			$(".song_lyric_container .song_lyric").css({
				marginTop:(-index+2)*30,
				
			});
			$(".only_song_lyric .song_lyric").css({
					marginTop:(-index+3)*60,
			});
			
		});
			};
		
		//9.监听声音按钮的点击
		$(".music_voice_icon").click(function(){
			//更改图标
			$(this).toggleClass("music_voice_icon2");
			//音量进度条变量
			//高亮范围
			var $voiceLine = $(".music_voice_bar .music_voice_line");
			//圆点位置
			var $voiceDot = $(".music_voice_line .music_voice_dot");
			//声音的切换
			if($(this).attr("class").indexOf("music_voice_icon2") != -1){
				//变为没有声音
				player.musicVoiceSeekTo(0);
				$voiceLine.css("width","0");
				$voiceDot.css("left","0");
				//$(this).addClass("music_voice_icon2");
				console.log("1");
				
			}
			else //if($(this).attr("class").indexOf("music_voice_icon2"))
			{
				//变为有声音
				player.musicVoiceSeekTo(1);
				$voiceLine.css("width","70");
				$voiceDot.css("left","70px");
				//$(this).removeClass("music_voice_icon2");
				console.log("2");
			}
			
		});
		
		//10.喜欢按钮点击事件
		$(".music_fav").click(function(){
			//判断当前歌曲喜欢状态
			$(this).toggleClass("music_fav2");
		
		});
		
		//11.点击纯净模式标签切换
		$(".music_only").click(function(){
			$(this).toggleClass("music_only2");
			$(".content_in").toggleClass('content_in_hide');
			$(".content_only_in").toggleClass('content_only_in2');
			
			//监听播放进度 同步歌词
			player.musicTimeUpdate(function(currentTime,duration,timeStr){
				//实现歌词的同步
				var index = lyric.currentIndex(currentTime);
				//找到歌词容器，将当前索引歌词标位高亮
				var $item = $(".only_song_lyric .song_lyric li").eq(index);
				$item.addClass("cur");
				$item.siblings().removeClass("cur");

				//设置歌词滚动
				//滚动ul
				if(index<= 2) return; //此时ul会向上滚 但是是整个向上 所以需要去结构中增加一个范围div
				$(".only_song_lyric .song_lyric").css({
					marginTop:(-index+3)*60,
				});
			});
			
		});
		
		//12空格点击播放暂停
		$(document).keydown(function(e){
			var e = e || window.event; //Space（空格键）	32
			if(e.keyCode == 32){
					//判断有没有播放过音乐
				if(player.currentIndex == -1){
					//没有播放过音乐
					$(".list_music").eq(0).find(".list_menu_play").trigger("click");

				}else{
					//已经播放过音乐
					$(".list_music").eq(player.currentIndex).find(".list_menu_play").trigger("click");
				}
			}
		});
		
		//13上下键控制音量
		$(document).keydown(function(e){
			var e = e || window.event;
			if(e.keyCode == 38){
				var width = $(".music_voice_line").width();
				if(width >= 70) return;
				width += 7;
				player.musicVoiceUp();//player里面增加函数
				$(".music_voice_line").css("width",width);
				$(".music_voice_dot").css("left",width);
			}
			if(e.keyCode == 40){
				var width = $(".music_voice_line").width();
				if(width <= 0) return;
				width -= 7;
				player.musicVoiceDown();
				$(".music_voice_line").css("width",width);
				$(".music_voice_dot").css("left",width);
			}
		});
		
		//14左右键控制进度条移动
		$(document).keydown(function(e){
			var e = e || window.event;
			if(e.keyCode == 37){
				player.musicMoveBack();
			}
			if(e.keyCode == 39){
				player.musicMoveOn();
			}
		});
		
		//15清除列表点击事件
		var clear_btn = $(".content_toolbar").find(".clear_musicList");
		clear_btn.click(function(){
			$(".content_list").remove();
		});

		//16全选按钮点击事件
		var check_all = $(".content_list_title .list_title .list_check");
		check_all.click(function(){
			//全选
			var $select_all = $(".list_music .list_check");
			$select_all.addClass("list_checked");
			
			if(check_all.attr("class").indexOf("list_checked") != -1){
				//全不选
				$select_all.removeClass("list_checked");
				
			}
		});
		
		//17顶部菜单栏的删除按钮点击事件
		$(".delete_music").click(function(){
			
			//删除一个是从界面上删除  后台的music[]也要删除
			//找到被选中的音乐
			var selected = $(".list_checked").parents(".list_music");
			//获取选中音乐的删除事件
			var del_select = selected.find(".list_menu_del");

			//还要判断删除的音乐是否为当前播放的 如果当前正在播放应该立即停止 并且自动切换播放到下一首
			//此处等于同时点击删除选中的歌曲
			//console.log(selected.length);
			for(var i=0;i<selected.length;i++){
				//判断是否有选中的歌
				if(selected.get(i).index==player.currentIndex){
					//当前删的是播放的
					//等于点击一下：下一首
					$(".music_next").trigger("click");
				}
				//删除对应索引的歌曲信息
				selected.get(i).remove();
				//歌曲列表删除
				player.changeMusic(selected.get(i).index);
			}
			
			
			//重新排序
			$(".list_music").each(function(index,ele){
				ele.index = index;
				$(ele).find(".list_number").text(index+1);
			});
			
		});
		
		//18音乐播放模式切换
		$(".music_mode").click(function(){
			if($(this).attr("class") == "music_mode"){
				$(this).removeClass("music_mode").addClass("music_mode2");
			}else if($(this).attr("class") == "music_mode2"){
				$(this).removeClass("music_mode2").addClass("music_mode3");
			}else if($(this).attr("class") == "music_mode3"){
				$(this).removeClass("music_mode3").addClass("music_mode4");
			}else if($(this).attr("class") == "music_mode4"){
				$(this).removeClass("music_mode4").addClass("music_mode");
			}
		});
		//播放模式方法
		player.musicTimeUpdate(function(currentTime,duration,timeStr){
				//循环播放
				if($("#MODE").attr("class") == "music_mode"){
					if(currentTime == duration){
						$(".list_music").eq(player.nextIndex()).find(".list_menu_play").trigger("click");
					}
				}
				//顺序播放
				if($("#MODE").attr("class") == "music_mode2"){
					if(currentTime == duration){
						//最后一首不循环获取当前索引与列表最后一首比较
						var Cycleindex = player.currentIndex+1;
						//暂停播放
						if(Cycleindex<player.musicList.length)
							{
								$(".list_music").eq(player.nextIndex()).find(".list_menu_play").trigger("click");
							}
					}
				}
				//随机播放
				if($("#MODE").attr("class") == "music_mode3"){
					if(currentTime == duration){
						var random = parseInt(Math.round(Math.random()*(player.musicList.length-1)));
						$(".list_music").eq(random).find(".list_menu_play").trigger("click");
						// console.log(random);
					}
				}
				//单曲循环
				if($("#MODE").attr("class") == "music_mode4"){
					if(currentTime == duration){
						player.music_single_cycle();
					}
				}
		});
		
		
	}

	
	//定义一个方法创建一个音乐(注意格式)
	function createMusicItem(index,music){
		var $item = $("<li class=\"list_music\">\n"+
							"<div class=\"list_check\"><i></i></div>\n"+
							"<div class=\"list_number\">"+(index+1)+"</div>\n"+
							"<div class=\"list_name\">"+music.name+"\n"+
								"<div class=\"list_menu\">\n"+
									"<a href=\"javascript:;\" title=\"播放\" class=\"list_menu_play\"></a>\n"+
									"<a href=\"javascript:;\" title=\"添加\"></a>\n"+
									"<a href=\"javascript:;\" title=\"下载\"></a>\n"+
									"<a href=\"javascript:;\" title=\"分享\"></a>\n"+
								"</div>\n"+
							"</div>\n"+
							"<div class=\"list_singer\">"+music.singer+"</div>\n"+
							"<div class=\"list_time\">\n"+
								"<span>"+music.time+"</span>\n"+
								"<a href=\"javascript:;\" title=\"删除\" class=\"list_menu_del\"></a>\n"+
							"</div>\n"+
						"</li>");
		
		//拿到原生的li
		//把每次传进来的对应的索引和音乐都绑定到原生的里上面
		$item.get(0).index = index;
		$item.get(0).music = music;
		
		return $item;
	}

});

```

### player.js (播放器设置)
```JavaScript
//player.js
//播放器

(function(){
	
	
	function Player($audio){
		
		return new Player.prototype.init($audio);
	}
	
	Player.prototype = {
		constructor:Player,
		
		musicList:[],
		//必要的参数
		init:function($audio){
			//获取jquery包装好的对象
			this.$audio = $audio;
			//下面get(0)是获取原生的
			this.audio = $audio.get(0);
		},
		
		//创建一个对象用于记录当前播放音乐的索引
		currentIndex:-1,
		playMusic:function(index,music){
			//判断点击的是否是同一首音乐
			if(this.currentIndex == index){
				//当前点击的是同一首歌
				//所以在播放与暂停间切换
				if(this.audio.paused){//拿到原生的li
					//音乐是暂停的
					this.audio.play();
					
				}else{
					//音乐不是暂停的
					this.audio.pause();
				}
			}else{
				//当前点击的为不是同一首歌
				//更换歌曲地址
				this.$audio.attr("src",music.link_url);
				this.audio.play();
				
				//不是同一首音乐：currentIndex要赋值为传进来的index
				this.currentIndex = index;
			}		
		},
		
		//专门定义一个变量来处理索引
		//上一首
		preIndex:function(){
			var index = this.currentIndex -1;
			if(index<0){
				index = this.musicList.length -1;
			}
			return index;
		},
		
 		//下一首
		nextIndex:function(){
			var index = this.currentIndex +1;
			if(index>this.musicList.length-1){
				index = 0;
			}
			return index;
		},
		
		//子菜单删除按钮
		changeMusic:function(index){
			
			//删除对应数据
			this.musicList.splice(index, 1);
			
			//判断删除的数据是否是正在播放音乐的前面音乐
			if(index < this.currentIndex){
				this.currentIndex = this.currentIndex -1;
			}
		},
		
		//获得音乐的播放时间(下面update已经使用 所以删除)
		/*getMusicDuration:function(){
			return this.audio.duration;
		},*/
		
		//获得音乐播放的当前进度(下面update已经使用 所以删除)
		/*getMusicCurrentTime:function(){
			return this.audio.currentTime;
		},*/
		
			musicTimeUpdate:function(callBack){//调用函数时候传递一个函数进来
				var $this = this;

				//8.监听播放的进度
				//ontimeupdate 事件在视频/音频（audio/video）当前的播放位置发送改变时触发。 audio 音频文件特有
				this.$audio.on("timeupdate",function(){

				//console.log(player.getMusicDuration(),player.getMusicCurrentTime());//打印测试成功！
				//总时间
				var duration = $this.audio.duration;
				//当前时间
				var currentTime = $this.audio.currentTime;

				//返回给我们需要的时间格式(默认格式是秒)
					//调用$this的format方法
				var timeStr = $this.formatDate(currentTime,duration);
				//console.log(timeStr); 打印测试 成功！
				//有bug：1.总时间会出现NAN 2.暂停后再次播放会归零后回到时间！！！！！！！！！
				
				callBack(currentTime,duration,timeStr);
			});
		},
		
		//格式化时间的方法
		formatDate:function(currentTime,duration){
			//计算总时长(和格式)
		var endMin = parseInt( duration /60);
		var endSec = parseInt( duration %60);
		
		if(endMin<10){
			endMin = "0"+endMin;
		}
		if(endSec<10){
			endSec = "0"+endSec;
		}
		
		//计算当前时长(和格式)
		var currentMin = parseInt( currentTime /60);
		var currentSec = parseInt( currentTime %60);
		
		if(currentMin<10){
			currentMin = "0"+currentMin;
		}
		if(currentSec<10){
			currentSec = "0"+currentSec;
		}
		
		return currentMin+":"+currentSec+" / "+endMin+":"+endSec;
		},
		
		//进度条拖拽音乐播放的方法
		musicSeekTo:function(value){
			//console.log(value);
			//发现value会有NaN值 
			if(isNaN(value)) return;//屏蔽掉NaN值 不报错
			
			//把当前时间 = 总时间*比例
			this.audio.currentTime = this.audio.duration*value;
			
		},
		
		//控制音量的进度条
		musicVoiceSeekTo:function(value){
			if(isNaN(value)) return;
			
			if(value<0 || value > 1 ) return;
			//设置音量 volum：取值为0-1
			this.audio.volume = value;
			
		},
		
		//键盘控制音量键方法
		musicVoiceUp:function(){
			this.audio.volume +=0.1;
		},
		
		musicVoiceDown:function(){
			this.audio.volume -=0.1;
		},
		
		//键盘音乐快进后退
		musicMoveOn: function(){
			this.audio.currentTime += 5;
		},
		musicMoveBack: function(){
			this.audio.currentTime -= 5;
		},
		
		//8.单曲循环
		music_single_cycle: function(){
			this.audio.currentTime = 0;
			this.audio.play();
		},
	
	}
	
	//对象初始化 把init的原型转化为player的原型 这样调用player时候就可以默认初始化了，不要单独调用init()了
	Player.prototype.init.prototype = Player.prototype;
	window.Player = Player;
	
	
})(window);




```

### progress.js (进度条设置)
```JavaScript
//progress.js
//对进度条进行操作

(function(){
	
	
	function Progress($progressBar,$progressLine,$progressDot){
		
		return new Progress.prototype.init($progressBar,$progressLine,$progressDot);
	}
	
	Progress.prototype = {
		constructor:Progress,
		
		musicList:[],
		//必要的参数
		init:function($progressBar,$progressLine,$progressDot){
			
			this.$progressBar = $progressBar;
			this.$progressLine = $progressLine;
			this.$progressDot = $progressDot;
		},
		
		//由于当前有两个第方可以控制进度条，一个是播放时自己不断刷新，一个是拖拽时会控制进度条，所有会产生冲突，所有创造一个新的类来解决
		isMove:false,//默认情况关闭
		
		
		//进度条的点击事件
		progressClick:function(callBack){
			//本质是设置前景高亮白色的宽度和设置小圆点的位置
			console.log("click事件-----------------");
			
			var $this = this;//此时此刻的this是progress
			
			//监听背景的点击(不能拖动)
			this.$progressBar.click(function(event){ //以下的this是progressBar 所以谁调用这个方法，谁就是this
				//获取背景距离窗口默认的位置
				var normalLeft = $(this).offset().left;
				//console.log(normalLeft);
				
				//获取点击的位置距离窗口默认的位置
				var eventLeft = event.pageX;
				//console.log(eventLeft);
				
				//设置前景的宽度
				$this.$progressLine.css("width",eventLeft-normalLeft);
				
				//设置小圆点的位置
				$this.$progressDot.css("left",eventLeft-normalLeft);
				
				//---------------------------------------------------------
				//由此开始设置手动拖拽进度条的方法
				//计算进度条的比例
				var value = (eventLeft-normalLeft)/$(this).width();
				//回调函数
				callBack(value);
			
				$(document).off("mousedown");
				$(document).off("mouseup");
				$(document).off("click");
			});
			
			
		},
		
		//进度条移动事件
		progressMove:function(callBack){
			
			var $this = this;
			//获取背景值的默认距离
			var normalLeft = this.$progressBar.offset().left;
			var eventLeft;
			//获取进度条宽度
			var barWidth = this.$progressBar.width();
				
			//拖拽事件：按下鼠标---鼠标移动---鼠标抬起
			//1.监听鼠标的按下事件
			this.$progressBar.mousedown(function(){
					
					//$(document).off("mouseup");
					
					//正在拖动进度条 （下面自动刷新进度条方法不执行）
					$this.isMove = true;
					
					//获取背景距离窗口默认的位置
					var normalLeft = $(this).offset().left;
				
					//获取点击时间
					firstTime = new Date().getTime();

					//2.监听鼠标的移动事件 (移动事件必须在按下事件中进行)
					//为了使交互更好，所以要在用户界面上都可以拖动 不仅仅局限于进度条范围内
					$(document).mousemove(function(event){

						//获取点击的位置距离窗口默认的位置
						eventLeft = event.pageX;

						//设置进度条的拖拽合法性
						var offset = eventLeft-normalLeft;
						//判断拖拽是否超出宽度
						if(offset >=0 && offset<= barWidth){

							//设置前景的宽度
							$this.$progressLine.css("width",eventLeft-normalLeft);

							//设置小圆点的位置
							$this.$progressDot.css("left",eventLeft-normalLeft);
						}
						
				//3.监听鼠标的抬起事件  //此处产生新问题  click事件会与mouseup事件产生冲突(解决：放在mousemove状态下 最后加上事件$(document).off("mouseup");)	
				$(document).mouseup(function(){
				//就是移除鼠标移动的事件
				$(document).off("mousemove");
				//注意要去css里面修改一下样式 不然拖拽到外部会选中内容 user-select：none；
				
				//抬起鼠标重新改为false
				$this.isMove = false;
					//---------------------------------------------------------
					//由此开始设置手动拖拽进度条的方法
					//计算进度条的比例
					var value = (eventLeft-normalLeft)/$this.$progressBar.width();
					//回调函数
					callBack(value);
					//结束鼠标抬起事件 释放鼠标：不然点击一次界面就会一直重复调用
					$(document).off("mouseup");
				});
					
				});
				console.log("mousemove工具");
				
			});
			
			
			//3.监听鼠标的抬起事件  //此处产生新问题  click事件会与mouseup事件产生冲突
			$(document).mouseup(function(){
					
				//就是移除鼠标移动的事件
				$(document).off("mousemove");
				//注意要去css里面修改一下样式 不然拖拽到外部会选中内容 user-select：none；
				
				//抬起鼠标重新改为false
				$this.isMove = false;
					//---------------------------------------------------------
					//由此开始设置手动拖拽进度条的方法
					//计算进度条的比例
					var value = (eventLeft-normalLeft)/$this.$progressBar.width();
					//回调函数
					callBack(value);

			});
			
			
	
		},
		
		
		
		//监听进度条百分比
		setProgress:function(value){
			
			//判断是否存在拖拽行为 
			if(this.isMove) return;//如果正在进行拖拽就不执行自动刷新进度条功能
			
			//不符合需求时
			if(value<0 || value>100) return;
			
			//符合需求时
			//进度条前景高亮部分设置
			this.$progressLine.css({
				
				width:value+"%",
				
			});
			
			//进度条圆点
			this.$progressDot.css({
				
				left:value+"%",
				
			});
		
		}
		
	}
	
		
	//对象初始化 把init的原型转化为player的原型 这样调用player时候就可以默认初始化了，不要单独调用init()了
	Progress.prototype.init.prototype = Progress.prototype;
	window.Progress = Progress;
	
	
})(window);










```

### lyric.js (歌词信息处理)
```JavaScript
//lyric.js
//对歌词进行操作


(function(){
	
	
	function Lyric(path){
		
		return new Lyric.prototype.init(path);
	}
	
	Lyric.prototype = {
		constructor:Lyric,
		
		musicList:[],
		//必要的参数
		init:function(path){
			this.path = path;
			
		},
		
		//存放时间的数组
		times:[],
		
		//存放歌词的数组
		lyrics:[],
		
		//定义一个索引 与歌词时间索引来匹配
		index:-1,
		
		loadLyric:function(callBack){
			var $this = this;
			
			$.ajax({
			
				url:$this.path,
				dataType:"text",
				success:function(data){
					//console.log(data);
					
					//解析歌词
					$this.parseLyric(data);
					//解析完歌词执行回调函数
					callBack();

				},
				error:function(e){
					//打印错误内容
					console.log(e);
					
				}
			});	
		},
		
		parseLyric:function(data){
			
			var $this = this;
			//清空歌曲信息 这样方便切歌时候更新信息 清空上一首歌的歌词和时间
			$this.times =[];
			$this.lyrics = [];
			
			var array = data.split("\n");
			//console.log(array);
			
			//利用正则表达式匹配时间[02:02.08]
			var timeReg = /\[(\d*:\d*\.\d*)\]/; //括号（）里面为所需要的时间
			
			//遍历取出每一行歌词
			$.each(array,function(index,ele){
				//存放歌词
				var lrc = ele.split("]")[1];
				//排除空字符串(没有歌词的)
				if(lrc.length <= 1) return true;
				$this.lyrics.push(lrc);
				
				//console.log(ele);
				
				var res = timeReg.exec(ele);
				//console.log(res);
				if(res ==null) return true;
				var timeStr = res[1]; //格式为02:02.08
				
				var res2 = timeStr.split(":");
				var min = parseInt(res2[0])*60;
				var sec = parseFloat(res2[1]);
				var time =parseFloat(Number(min+sec).toFixed(2)); 
				
				//存放当前转化好的时间
				$this.times.push(time);
				
			});
			//console.log($this.times,$this.lyrics);
		},
		
		//获取当前的索引
		currentIndex:function(currentTime){
			//console.log(currentTime);
			if(currentTime>= this.times[0]){
				this.index++;
				this.times.shift();//数组的shift方法是专门来删除数组最前面的一个元素
			}
			return this.index;
		}	
	}
	
	//对象初始化 把init的原型转化为player的原型 这样调用player时候就可以默认初始化了，不要单独调用init()了
	Lyric.prototype.init.prototype = Lyric.prototype;
	window.Lyric = Lyric;
	
	
})(window);

```

# 谢谢观看！

