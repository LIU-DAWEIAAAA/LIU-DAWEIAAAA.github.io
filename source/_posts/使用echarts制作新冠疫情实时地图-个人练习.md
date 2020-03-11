---
title: 使用echarts制作新冠疫情实时地图(个人练习)
date: 2020-03-10 17:12:40 
valineenbale: true 
declare: true
tags: [echarts,学习记录]
---
# echarts制作新冠疫情实时地图
>2020年突如其来的肺炎疫情，打乱了很多人的计划，也让很多人措手不及。对于绝大多数人来说，老老实实待在家里不出门，就是自我保护的最好措施，也是为国家做贡献的最有效方式。

>从23号武汉封城，到全国多地纷纷效仿，最后到一级响应预警，“不拜年不聚会不串门”已经成了整个社会的共识。14亿人都只能闭门不出，与世隔绝。这是件奇怪又可怕的坏事，也是某种意义上的“好事”：因为全民族都静下心来，正是一个国家深度思考的开始，也是一个国家走向强大的征兆。

疫情期间，在家闭门不出的我看到了与我年纪一般大的同龄人奋斗在抗击疫情的第一线。在QQ空间里看到那些早已不联系的小学初中同学，他们有的竟然已经穿上白衣战袍驰援武汉，有的前往高速路口成为了一名关卡的志愿者帮助过往车辆的驾驶员测量体温，还有的前往社区帮助居委会宣传与落实疫情防护工作。作为一名大学生的我也没有理由不为这疫情做些什么。正好看到学校3月2号发的通知，要求我们可以结合自身所学专业绘制一张疫情地图，于是我开始上网查阅相关信息，结合上学期所学的一些web技术开始了这一次的项目试手。

<!-- more -->

## 1.引用资料
echarts官方文档：https://www.echartsjs.com/zh/index.html

B站UP主：大麦茶18 av92128036

B站up主：武汉黑马程序员 av89983523

CSDN博客： https://blog.csdn.net/lifhcode/article/details/81221693

简书：https://www.jianshu.com/p/a9268ee8771e

围观大神项目：https://www.mapbox.cn/coronavirusmap/

## 2.开始实践
### 2.1 第一次尝试（初步完成）
首先看up主大麦茶18的视频，由于作者已经写好爬虫脚本，我们只要照着up主的操作一步一步实现即可。到此初步认识了echarts。这个项目可以正常运行，没有跨域影像，不需要部署tomcat服务器。

代码如下：

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>疫情地图展示</title>
  <style>
    .container {
      width: 1200px;
      margin: 0 auto;
    }

    #myEcharts {
      width: 800px;
      height: 500px;
      border: solid 1px red;
      margin: 0 auto;
    }
  </style>
  <script src="https://www.echartsjs.com/examples/vendors/jquery/jquery.js"></script>
  <!-- 引入 echarts.js -->
  <script src="https://www.echartsjs.com/examples/vendors/echarts/echarts.min.js?_v_=1578305236132"></script>
  <!--引入中国的地图数据js文件，引入后会自动注册地图名字和数据-->
  <script src="https://www.echartsjs.com/examples/vendors/echarts/map/js/china.js?_v_=1578305236132"></script>

</head>

<body>
  <div class="container">
    <h3>累计确诊人数如下：</h3>
    <!--为echarts准备一个dom容器-->
    <div id="myEcharts"></div>
  </div>


  <script>
    //初始化echarts实例
    var myChart = echarts.init(document.getElementById('myEcharts'));
    // 指定图表的配置项和数据
    option = {
      title: {
        text: '中国疫情图',
        left: 'center'
      },
      tooltip: {
        trigger: 'item'
      },
      legend: {
        orient: 'vertical',
        left: 'left',
        data: ['中国疫情图']
      },
      visualMap: {
        type: 'piecewise',
        pieces: [
          { min: 1000, max: 1000000, label: '大于等于1000人', color: '#372a28' },
          { min: 500, max: 999, label: '确诊500-999人', color: '#4e160f' },
          { min: 100, max: 499, label: '确诊100-499人', color: '#974236' },
          { min: 10, max: 99, label: '确诊10-99人', color: '#ee7263' },
          { min: 1, max: 9, label: '确诊1-9人', color: '#f5bba7' },
        ],
        color: ['#E0022B', '#E09107', '#A3E00B']
      },
      toolbox: {
        show: true,
        orient: 'vertical',
        left: 'right',
        top: 'center',
        feature: {
          mark: { show: true },
          dataView: { show: true, readOnly: false },
          restore: { show: true },
          saveAsImage: { show: true }
        }
      },
      roamController: {
        show: true,
        left: 'right',
        mapTypeControl: {
          'china': true
        }
      },
      series: [
        {
          name: '确诊数',
          type: 'map',
          mapType: 'china',
          roam: false,
          label: {
            show: true,
            color: 'rgb(249, 249, 249)'
          },
          data: []
        }
      ]
    };

    //使用指定的配置项和数据显示图表
    myChart.setOption(option);

    //获取数据
    function getData() {
      $.ajax({
        url: "https://view.inews.qq.com/g2/getOnsInfo?name=disease_h5",
        dataType: "jsonp",
        success: function (data) {
          //  console.log(data.data)
          var res = data.data || "";
          res = JSON.parse(res);
          var newArr = [];
          //newArr的数据格式为：
          // [{
          //   name: '北京11',
          //   value: 212
          // }, {
          //   name: '天津',
          //   value: 60
          // }]
          if (res) {
            //获取到各个省份的数据
            var province = res.areaTree[0].children;
            for (var i = 0; i < province.length; i++) {
              var json = {
                name: province[i].name,
                value: province[i].total.confirm
              }
              newArr.push(json)
            }
            console.log(newArr)
            console.log(JSON.stringify(newArr))
            //使用指定的配置项和数据显示图表
            myChart.setOption({
              series: [
                {
                  name: '确诊数',
                  type: 'map',
                  mapType: 'china',
                  roam: false,
                  label: {
                    show: true,
                    color: 'rgb(249, 249, 249)'
                  },
                  data: newArr
                }
              ]
            });
          }
        }

      })
    }
    getData();

  </script>
</body>
</html>
```
效果图如下：

{% asset_img echarts第一次尝试.gif  %}

### 2.2 第二次尝试（完成数据爬取）
开始第二次尝试 本次尝试参考up主：武汉黑马程序员

通过这个视频了解安装了node.js、npm、并且使用npm从官方下载了echarts的相关组件到本地。

首先老师写了一段爬取数据的程序：
```Javascript
// JavaScript Document

//目标：爬取丁香园网站疫情的数据
//在node端要有一个帮助我请求丁香园网站

const superagent = require('superagent');
//解析数据
const cheerio = require('cheerio');

const fs = require('fs');
const path = require('path');

//superagent.get(url目标网站).then()
//1.要在node上请求目标网站
const url='https://ncov.dxy.cn/ncovh5/view/pneumonia';

superagent.get(url).then( res => {
	//请求成功
	//console.log(res.text); //响应的内容
	//浏览器可以解析html 但是node端是不行的
	
	//2.去解析html字符串，从里面去提取疫情数据
	const $ =cheerio.load(res.text);
	//获取全国疫情数据 利用标签的id
	var $getListByCountryTypeService1 = $('#getListByCountryTypeService1').html();
	
	//获取各省地区的疫情数据
	var $getAreaStat = $('#getAreaStat').html();
	//获取增长的情况
	var $getStatisticsService = $('#getStatisticsService').html();
	
	//console.log($getListByCountryTypeService1);//已经得到各省的实时数据
	//接下来操作得到的字符串
	//使用字符串切割，或者使用正则匹配 eval()函数
	//使用eval 先定义一个对象
	var dataObj = {};
	eval($getListByCountryTypeService1.replace(/window/g,'dataObj')); //因为node（node.js）里面没有window 所以那dataObj替换
	eval($getAreaStat.replace(/window/g,'dataObj'));
	eval($getStatisticsService.replace(/window/g,'dataObj'));
	console.log(dataObj);
	
	//3.fs写入数据到本地
	fs.writeFile(path.join(__dirname,'./data.json'),JSON.stringify(dataObj),err =>{
		//错误回调 有错报错
		if(err) throw err;
		//没错打印结果
		console.log("数据写入成功！");
	});//要把对象转换为json写入
	
	
})
.catch(err => {
	throw err;
});
```
需要用node.js运行 运行代码为：node .\spider.js 同时本地会生成一个data.json文件

{% asset_img  echarts尝试爬取数据.gif %}

其中最重要的是对数据的解析，本次采用eval()函数对字符串进行分割提取所需要的数据。

接下来用express模块来创建一个服务器，用fs模块来读取数据，要详细了解应去深入学习node.js。

```JavaScript
// JavaScript Document

//1.引入express
const express = require('express');
//读取json文件
const fs = require('fs');
const path = require('path');
//解决跨域问题!!!!! npm install cors -p
const cors = require('cors');

//2.创建服务器应用
const app = express();

//3.监听请求
app.use(cors());//解决跨域问题！！！ 
app.get('/api/data',(req,res) => {
	//要将爬取到的json数据返回
	fs.readFile(path.join(__dirname,'./data.json'),'utf8',(err,data)=>{
		if (err) throw err;
		console.log(data);
		
		//返回数据
		res.send(data);	
	});
	
	
});

//4.分配端口号启动服务
app.listen(8066,() =>{
	console.log('当前服务器启动成功 http://127.0.0.1:8066/api/data');
});
```
使用时应启用node:代码为：node .\index.js 

（必须启动index.js 才可以打开index.html  否侧获取不到数据）
主题index.html代码如下：

```HTML
<!doctype html>	
<html>
<head>
<meta charset="utf-8">
<title>echarts测试2 ncov全国分布图</title>
	<!--1.引入echarts-->
	<script type="text/javascript" src="js/echarts.min.js"></script>
	
	<!--引入中国地图-->
	<script type="text/javascript" src="js/china.js"></script>
	
	<!--设置容器样式-->
	<style>
		#container{
			width: 800px;
			height: 600px;
		}
	</style>
</head>

<body>
	<!--2.存放echarts的容器-->
	<div id="container" ></div>
	
	<!--将图标配置运用到当前容器中-->
	<script>
		//将图表实例化和容器关联
		var myChart = echarts.init(document.querySelector('#container'));
		
			var filterData = [];//设置空数组存放数据
				//使用fetch方法获取请求
				//fetch 不需要引入任何的文件<br>
				//fetch（请求的url，）
				fetch('http://127.0.0.1:8066/api/data')  //发起请求
				.then(res => res.json())  //把可读的数据流转化为json格式
				.then(res => {
					//console.log(res); //获取到的疫情数据
					//var getListByCountryTypeServicel = res.getListByCountryTypeServicel;
					//获取省的数据
					var getListByCountryTypeService1 = res.getListByCountryTypeService1;
					//将接口返回的数据进行处理 转为echarts认可的数据  item代表里面的对象 每次循环往后推一个
					
					getListByCountryTypeService1.forEach(item => {
						filterData.push({
							name:item.provinceShortName, //省名
							value:item.confirmedCount,//确诊人数
							deadCount:item.deadCount,//死亡人数
							curedCount:item.curedCount,//治愈人数
						});
					});

					console.log(filterData);// {name: "湖北", value: 67332, deadCount: 2871, curedCount: 38557}
					//配置图表数据信息与应用
					myChart.setOption({
						//设置标题
						title:{
							//标题相关的配置 
							text:'全国疫情分布图' ,
							//设置副标题
							subtext:'制作人：南通大学——地理信息171——刘大炜',
							//设置标题的位置
							left:"center",
						},
						//图表的背景颜色
						backgroundColor:"#f7f7f7",
						//设置图例样式
						visualMap:{
							type:'piecewise',//分段图例 'continue'为连续
							pieces: [
								{gt: 10000},            // (10000, Infinity]
								{gt: 1000, lte: 9999},  // (1000, 9999]
								{gt: 100, lte: 999},  // (100, 999]
								{gt: 10, lte: 99},   // (10, 99]
								{gt: 0, lte: 9},       // (0, 9]
							],
							inRange: {
								color: ['rgb(250,235,210)', 'rgb(233,161,136)', 'rgb(213,99,85)','rgb(150,57,40)','rgb(72,15,16)'],
							}
						},
						//设置鼠标悬停的样式
						tooltip:{
							formatter:function(params){ 
								console.log(params,'formatter');
								return '地区：'+(params.name||"南海诸岛(暂无数据)")+'<br/>确诊：'+(params.value || 0)+'人<br/>治愈：'+(((params.data) && (params.data.curedCount ))|| 0)+'人<br/>死亡：'+((params.data) &&(params.data.deadCount) || 0)+'人<br/>'
							}
						},

						//开始设置中国地图
						series:[{
							//当前图表的类型
							type:'map',
							//map的属性 当前地图是中国地图
							map:'china',
							//设置地图上的文字
							label:{
								//覆盖物设置
								show:true,
							},
							data:filterData

						}],
					});
				});
	
	</script>
	
</body>
</html>
```

最终效果图如下:


{% asset_img echarts第二次尝试结果图.gif %}

### 2.3 尝试地图下钻并完善功能（显示世界、中国各省、各市）
查阅资料，尝试地图下钻，提取出中国的省级地图，获取地级市数据（可以实时更新，但是数据的城市名称与echarts官方地图的城市名称有所不同，所以游戏地区显示为暂无数据），尝试世界地图（所用的接口暂时无法实时更新世界疫情数据）

该项目需要调用本地的js文件（本次项目省份数据采用js数据格式，echarts官方提供js与json两种数据格式，但是json格式中省份、城市名称为邮政编码，与网络数据较难匹配），需要用到跨域知识（当前实验中仅做了解，还未深入学习），所以需要部署在tomcat上

地图下钻的显示：本次项目采用的是重复调用js文件，性能不是很好，后面有时间可以对其进行整合与改进。

主要代码如下：（内采用jQuery脚本）

1. jquery-3.4.1.js
2. Convert_Pinyin.js(将中文字符转化为拼音)

```HTML
<!doctype html>
<html>
<head>
<meta charset="utf-8">
<title>地图钻取测试3</title>
<style>
* {
	user-select: none;
}
body {
	background-image: url("asset/images/background.png");
}
/*标题样式*/
.title {
	width: 100%;
	height: 10vh;
	text-align: center;
	color: #fff;
	font-size: 2em;
	line-height: 10vh;
}
.box {
	position: absolute;
	width: 80%;
	height: 80vh;
	left: 10%;
	top: 10%;
	border: 1px solid green;
}
/*地图范围 */
.chart {
	position: relative;
	height: 90%;
	top: 3%;
}
button {
	top: 0;
	background-color: #00C298;
	border: 0;
	float: left;
	margin-right: 1em;
	color: #fff;
	height: 27px;
	font-family: Microsoft Yahei;
	font-size: 1em;
	cursor: pointer;
	opacity: 0.7;
}
button:hover {
	opacity: 1;
	transform: scale(1.2);
}
</style>
<script src="js/jquery-3.4.1.js"></script> 
<!--引入文字转拼音工具--> 
<script src="js/Convert_Pinyin.js"></script> 
<!-- 引入 echarts.js --> 
<script src="js/echarts.min.js"></script> 
<!--引入中国的地图数据js文件，引入后会自动注册地图名字和数据--> 
<script src="js/china.js"></script> 
<script src="js/map/js/province/jiangsu.js"></script> 
<script src="js/map/js/world.js"></script>
</head>

<body>
<!--标题-->
<div class="title">COVID-19新型冠状病毒肺炎即时分布图</div>
<div class="box"> 
  <!--按钮-->
  <button class= "worldBtn" onclick="backWorld()">查看世界地图</button>
  <!--按钮-->
  <button class= "backBtn" onclick="backChina()">返回全国地图</button>
  <!--为echarts准备一个dom容器-->
  <div id="myEcharts" class="chart"></div>
</div>
<script>
    //初始化echarts实例
    var myChart = echarts.init(document.getElementById('myEcharts'));
	
	 //获取数据各省数据
   /* function getData() {
      $.ajax({
		//ajax调用
        url: "https://view.inews.qq.com/g2/getOnsInfo?name=disease_h5",
        dataType: "jsonp",
        success: function (data) {
          //console.log(data.data)
          var res = data.data || "";
          res = JSON.parse(res);
          var newArr = []; //{name: "辽宁", value: 125, curedCount: 106, deadCount: 1}
          
          if (res) {
            //获取到各个省份的数据
            var province = res.areaTree[0].children;
            for (var i = 0; i < province.length; i++) {
              var json = {
                name: province[i].name,//省份名称
                value: province[i].total.confirm,//确诊人数
				curedCount:province[i].total.heal,//治愈人数
				deadCount:province[i].total.dead,//死亡人数
				  
              }
			  //新数组添加数据
              newArr.push(json)
            }
            //console.log(newArr)
            //console.log(JSON.stringify(newArr))
            //使用指定的配置项和数据显示图表 
			  
			//地图类型：'china' 地图数据：newArr
			echartsResetOption('china',newArr);
			
			  
          }
        }

      })
    }
    getData();*/
	backWorld();

//-----------------------------------------------------------------------------------------
	   //使用指定的配置项和数据显示图表
    //myChart.setOption(option);
	myChart.on('mouseover', function (params) {
                var dataIndex = params.dataIndex;
                console.log(dataIndex);
				console.log(params.name);
            });
	
     //下钻其实就是点击事件，切换脚本而已
     myChart.on('click', function (chinaParam) {
		
		 /*
		 //原理：unicode码的处理
		 //处理省份的文字转化拼音工具
		 function ConvertName(){
			 var keyword = document.getElementById('keyword').value;
			 var full = pinyin.getFullChars(keyword); //获取全写拼音
			 var simply = pinyin.getCamelChars(keyword); //获取简写拼音(提取首字母并大写)
			 document.getElementById('full').value = full;
			 document.getElementById('simply').value = simply;//默认大写.可通过string.toLowerCase()转成小写
		 }
		 */

		 //将省份中文改成拼音显示 配合地图js数据
		 var provincePingName = pinyin.getFullChars(chinaParam.name).toLowerCase();
		 console.log(provincePingName);
		 fileURL = "js/map/js/province/"+provincePingName+".js"
		 console.log(fileURL);
		 
		 //判断路径是否存在
		IsLoad(fileURL,function(res){
        if(res){
            alert('下级地图存在数据,请求的url可以访问');
				
				//用jquery引入js
				 $.get('js/map/js/province/'+provincePingName+'.js',function(){
					alert("已经添加了"+provincePingName+".js");
				 });
			
			 console.log("点击"+chinaParam.name+"！！");
			 var provindeID = chinaParam.dataIndex;
             var option = myChart.getOption();
			//配置各地区的地图  每个地图的名字都是chinaParam.name
             option.series[0].map = chinaParam.name;
             myChart.setOption(option); 
			
			if(provincePingName != 'zhongguo'){
			//获取数据省份下各市数据
			getProvinceData();
			function getProvinceData() {
			  $.ajax({
				//ajax调用
				url: "https://view.inews.qq.com/g2/getOnsInfo?name=disease_h5",
				dataType: "jsonp",
				success: function (data) {
				  //console.log(data.data)
				  var res = data.data || "";
				  res = JSON.parse(res);
				  var newProvinceArr = []; //{name: "常州", value: 125, curedCount: 106, deadCount: 1}

				  if (res) {
						//获取到对应省份下城市的数据
						var city = res.areaTree[0].children[provindeID].children;
						for (var i = 0; i < city.length; i++) {
							  var json = {
								name: city[i].name+"市",//城市名称 地图里面命名格式为XX(市) 所以要加上+"市"
								value: city[i].total.confirm,//确诊人数
								curedCount:city[i].total.heal,//治愈人数
								deadCount:city[i].total.dead,//死亡人数
							  }
							  //新数组添加数据
							  newProvinceArr.push(json)
							}
						console.log(newProvinceArr)
						//console.log(JSON.stringify(newProvinceArr))
						//使用指定的配置项和数据显示图表
						echartsResetOption(chinaParam.name,newProvinceArr);
						if(chinaParam.name != '湖北'){
							//不是湖北的省份单独设置分级标准
						myChart.setOption({
							visualMap: {
								type: 'piecewise',
									pieces: [
									  { min: 500,label: '大于等于500人', color: 'rgb(72,15,16)' },
									  { min: 100, max: 499, label: '确诊100-499人', color: 'rgb(150,57,40)' },
									  { min: 50, max: 99, label: '确诊50-99人', color: 'rgb(213,99,85)' },
									  { min: 10, max: 49, label: '确诊10-49人', color: 'rgb(233,161,136)' },
									  { min: 1, max: 9, label: '确诊1-9人', color: 'rgb(250,235,210)' },
									],
								},
							});
						  }
					 }

				  }
				})


    };
			}else{
				alert("中国！");
				 //获取数据各省数据
				backChina();
			}
			
			
        }else
			{
			alert('下级地图没有数据！不可访问!');
			
			}
    });//判断是否有数据结束
		 
     });//mychart.click事件结束
	 
	
	
	// 判断一个url是否可以访问
    function IsLoad(url,fun){
        $.ajax({
            url: url,
            type: 'GET',
            success: function(response) {
                if($.isFunction(fun)){
                    fun(true);
                }
            },
            error:function () {
                if($.isFunction(fun)){
                    fun(false);
                }
            }
        });
    }
   
	
	/**
 * 返回全国地图
 */
function backChina() {

	 //获取数据各省数据
    function getData() {
      $.ajax({
		//ajax调用
        url: "https://view.inews.qq.com/g2/getOnsInfo?name=disease_h5",
        dataType: "jsonp",
        success: function (data) {
          //console.log(data.data)
          var res = data.data || "";
          res = JSON.parse(res);
          var newArr = []; //{name: "辽宁", value: 125, curedCount: 106, deadCount: 1}
          
          if (res) {
            //获取到各个省份的数据
            var province = res.areaTree[0].children;
            for (var i = 0; i < province.length; i++) {
              var json = {
                name: province[i].name,//省份名称
                value: province[i].total.confirm,//确诊人数
				curedCount:province[i].total.heal,//治愈人数
				deadCount:province[i].total.dead,//死亡人数
				  
              }
			  //新数组添加数据
              newArr.push(json)
            }
            //console.log(newArr)
            //console.log(JSON.stringify(newArr))
            //使用指定的配置项和数据显示图表
			//中国地图  地图类型china 地图数据 newArr
            echartsResetOption('china',newArr);
			myChart.setOption({
				title: {
					text: '实时中国疫情图',
					subtext:'制图人：南通大学——地理信息171——刘大炜',
					left: 'center'
      			},
			});
          }
        }

      })
    }
    getData();

}
	

/*
*返回世界地图
*/
function backWorld(){
	 //获取世界数据
    function getworldData() {
      $.ajax({
		//ajax调用
        url: "https://view.inews.qq.com/g2/getOnsInfo?name=disease_h5",
        dataType: "jsonp",
        success: function (data) {
          console.log(data.data)
          var res = data.data || "";
          res = JSON.parse(res);
          var newArr = []; //{name: "辽宁", value: 125, curedCount: 106, deadCount: 1}
          
          if (res) {
            //获取到各个省份的数据
            var world = res.areaTree;
            for (var i = 0; i < world.length; i++) {
              var json = {
                name: world[i].name,//国家名称
                value: world[i].total.confirm,//确诊人数
				curedCount:world[i].total.heal,//治愈人数
				deadCount:world[i].total.dead,//死亡人数
				newCount:world[i].today.confirm,//今日新增
				  
              }
			  //新数组添加数据
              newArr.push(json)
            }
            console.log(newArr)
            console.log(JSON.stringify(newArr))
            //使用指定的配置项和数据显示图表
			//地图类型 world  地图数据 newArr
			  echartsResetOption('world',newArr);
			  //单独设置世界地图属性
			  myChart.setOption({
				//标题 
				title: {
					text: '实时世界疫情图',
					subtext:'制图人：南通大学——地理信息171——刘大炜',
					left: 'center'
      			},
				//不显示地区名字
				series:{
					label:false,//不显示地区名字
					
				},
				nameMap:{//echarts的世界地图为英文 数据为中文 所以要写对应关系
					"Canada": "加拿大",
					"Turkmenistan": "土库曼斯坦",
					"Saint Helena": "圣赫勒拿",
					"Lao PDR": "老挝",
					"Lithuania": "立陶宛",
					"Cambodia": "柬埔寨",
					"Ethiopia": "埃塞俄比亚",
					"Faeroe Is.": "法罗群岛",
					"Swaziland": "斯威士兰",
					"Palestine": "巴勒斯坦",
					"Belize": "伯利兹",
					"Argentina": "阿根廷",
					"Bolivia": "玻利维亚",
					"Cameroon": "喀麦隆",
					"Burkina Faso": "布基纳法索",
					"Aland": "奥兰群岛",
					"Bahrain": "巴林",
					"Saudi Arabia": "沙特阿拉伯",
					"Fr. Polynesia": "法属波利尼西亚",
					"Cape Verde": "佛得角",
					"W. Sahara": "西撒哈拉",
					"Slovenia": "斯洛文尼亚",
					"Guatemala": "危地马拉",
					"Guinea": "几内亚",
					"Dem. Rep. Congo": "刚果（金）",
					"Germany": "德国",
					"Spain": "西班牙",
					"Liberia": "利比里亚",
					"Netherlands": "荷兰",
					"Jamaica": "牙买加",
					"Solomon Is.": "所罗门群岛",
					"Oman": "阿曼",
					"Tanzania": "坦桑尼亚",
					"Costa Rica": "哥斯达黎加",
					"Isle of Man": "曼岛",
					"Gabon": "加蓬",
					"Niue": "纽埃",
					"Bahamas": "巴哈马",
					"New Zealand": "新西兰",
					"Yemen": "也门",
					"Jersey": "泽西岛",
					"Pakistan": "巴基斯坦",
					"Albania": "阿尔巴尼亚",
					"Samoa": "萨摩亚",
					"Czech Rep.": "捷克",
					"United Arab Emirates": "阿拉伯联合酋长国",
					"Guam": "关岛",
					"India": "印度",
					"Azerbaijan": "阿塞拜疆",
					"N. Mariana Is.": "北马里亚纳群岛",
					"Lesotho": "莱索托",
					"Kenya": "肯尼亚",
					"Belarus": "白俄罗斯",
					"Tajikistan": "塔吉克斯坦",
					"Turkey": "土耳其",
					"Afghanistan": "阿富汗",
					"Bangladesh": "孟加拉国",
					"Mauritania": "毛里塔尼亚",
					"Dem. Rep. Korea": "朝鲜",
					"Saint Lucia": "圣卢西亚",
					"Br. Indian Ocean Ter.": "英属印度洋领地",
					"Mongolia": "蒙古",
					"France": "法国",
					"Cura?ao": "库拉索岛",
					"S. Sudan": "南苏丹",
					"Rwanda": "卢旺达",
					"Slovakia": "斯洛伐克",
					"Somalia": "索马里",
					"Peru": "秘鲁",
					"Vanuatu": "瓦努阿图",
					"Norway": "挪威",
					"Malawi": "马拉维",
					"Benin": "贝宁",
					"St. Vin. and Gren.": "圣文森特和格林纳丁斯",
					"Korea": "韩国",
					"Singapore": "新加坡",
					"Montenegro": "黑山共和国",
					"Cayman Is.": "开曼群岛",
					"Togo": "多哥",
					"China": "中国",
					"Heard I. and McDonald Is.": "赫德岛和麦克唐纳群岛",
					"Armenia": "亚美尼亚",
					"Falkland Is.": "马尔维纳斯群岛（福克兰）",
					"Ukraine": "乌克兰",
					"Ghana": "加纳",
					"Tonga": "汤加",
					"Finland": "芬兰",
					"Libya": "利比亚",
					"Dominican Rep.": "多米尼加",
					"Indonesia": "印度尼西亚",
					"Mauritius": "毛里求斯",
					"Eq. Guinea": "赤道几内亚",
					"Sweden": "瑞典",
					"Vietnam": "越南",
					"Mali": "马里",
					"Russia": "俄罗斯",
					"Bulgaria": "保加利亚",
					"United States": "美国",
					"Romania": "罗马尼亚",
					"Angola": "安哥拉",
					"Chad": "乍得",
					"South Africa": "南非",
					"Fiji": "斐济",
					"Liechtenstein": "列支敦士登",
					"Malaysia": "马来西亚",
					"Austria": "奥地利",
					"Mozambique": "莫桑比克",
					"Uganda": "乌干达",
					"Japan": "日本本土",
					"Niger": "尼日尔",
					"Brazil": "巴西",
					"Kuwait": "科威特",
					"Panama": "巴拿马",
					"Guyana": "圭亚那",
					"Madagascar": "马达加斯加",
					"Luxembourg": "卢森堡",
					"American Samoa": "美属萨摩亚",
					"Andorra": "安道尔",
					"Ireland": "爱尔兰",
					"Italy": "意大利",
					"Nigeria": "尼日利亚",
					"Turks and Caicos Is.": "特克斯和凯科斯群岛",
					"Ecuador": "厄瓜多尔",
					"U.S. Virgin Is.": "美属维尔京群岛",
					"Brunei": "文莱",
					"Australia": "澳大利亚",
					"Iran": "伊朗",
					"Algeria": "阿尔及利亚",
					"El Salvador": "萨尔瓦多",
					"C?te d'Ivoire": "科特迪瓦",
					"Chile": "智利",
					"Puerto Rico": "波多黎各",
					"Belgium": "比利时",
					"Thailand": "泰国",
					"Haiti": "海地",
					"Iraq": "伊拉克",
					"S?o Tomé and Principe": "圣多美和普林西比",
					"Sierra Leone": "塞拉利昂",
					"Georgia": "格鲁吉亚",
					"Denmark": "丹麦",
					"Philippines": "菲律宾",
					"S. Geo. and S. Sandw. Is.": "南乔治亚岛和南桑威奇群岛",
					"Moldova": "摩尔多瓦",
					"Morocco": "摩洛哥",
					"Namibia": "纳米比亚",
					"Malta": "马耳他",
					"Guinea-Bissau": "几内亚比绍",
					"Kiribati": "基里巴斯",
					"Switzerland": "瑞士",
					"Grenada": "格林纳达",
					"Seychelles": "塞舌尔",
					"Portugal": "葡萄牙",
					"Estonia": "爱沙尼亚",
					"Uruguay": "乌拉圭",
					"Antigua and Barb.": "安提瓜和巴布达",
					"Lebanon": "黎巴嫩",
					"Uzbekistan": "乌兹别克斯坦",
					"Tunisia": "突尼斯",
					"Djibouti": "吉布提",
					"Greenland": "格陵兰",
					"Timor-Leste": "东帝汶",
					"Dominica": "多米尼克",
					"Colombia": "哥伦比亚",
					"Burundi": "布隆迪",
					"Bosnia and Herz.": "波斯尼亚和黑塞哥维那",
					"Cyprus": "塞浦路斯",
					"Barbados": "巴巴多斯",
					"Qatar": "卡塔尔",
					"Palau": "帕劳",
					"Bhutan": "不丹",
					"Sudan": "苏丹",
					"Nepal": "尼泊尔",
					"Micronesia": "密克罗尼西亚",
					"Bermuda": "百慕大",
					"Suriname": "苏里南",
					"Venezuela": "委内瑞拉",
					"Israel": "以色列",
					"St. Pierre and Miquelon": "圣皮埃尔和密克隆群岛",
					"Central African Rep.": "中非",
					"Iceland": "冰岛",
					"Zambia": "赞比亚",
					"Senegal": "塞内加尔",
					"Papua New Guinea": "巴布亚新几内亚",
					"Trinidad and Tobago": "特立尼达和多巴哥",
					"Zimbabwe": "津巴布韦",
					"Jordan": "约旦",
					"Gambia": "冈比亚",
					"Kazakhstan": "哈萨克斯坦",
					"Poland": "波兰",
					"Eritrea": "厄立特里亚",
					"Kyrgyzstan": "吉尔吉斯斯坦",
					"Montserrat": "蒙特塞拉特",
					"New Caledonia": "新喀里多尼亚",
					"Macedonia": "马其顿",
					"Paraguay": "巴拉圭",
					"Latvia": "拉脱维亚",
					"Hungary": "匈牙利",
					"Syria": "叙利亚",
					"Honduras": "洪都拉斯",
					"Myanmar": "缅甸",
					"Mexico": "墨西哥",
					"Egypt": "埃及",
					"Nicaragua": "尼加拉瓜",
					"Cuba": "古巴",
					"Serbia": "塞尔维亚",
					"Comoros": "科摩罗",
					"United Kingdom": "英国",
					"Fr. S. Antarctic Lands": "南极洲",
					"Congo": "刚果（布）",
					"Greece": "希腊",
					"Sri Lanka": "斯里兰卡",
					"Croatia": "克罗地亚",
					"Botswana": "博茨瓦纳",
					"Siachen Glacier": "锡亚琴冰川地区"
				},
				//设置鼠标悬停的样式
				tooltip:{
					formatter:function(params){ 
						console.log(params,'formatter');
						return '地区：'+(params.name || "暂无数据")+'<br/>确诊：'+(params.value || 0)+'人<br/>治愈：'+(((params.data) && (params.data.curedCount ))|| 0)+'人<br/>死亡：'+((params.data) &&(params.data.deadCount) || 0)+'人<br/>今日新增：'+((params.data) &&(params.data.newCount)|| 0)+'人<br/>'
					}
				},
				
            });
          }
        }

      })
    }
    getworldData();
}

	
/*
*   函数名称：echartsResetOption(mapName,mapArr)
*	mapName:地图名称
*	mapArr:地图上的数据
*
*/
function echartsResetOption(mapName,mapArr){
	
	myChart.setOption({
			//标题
             title: {
        		text: '实时'+mapName+'疫情图',
				 textStyle:{
					 fontWeight:'bolder',
					 fontSize:25,
					 color:'#eee' 
				 },
				subtext:'制图人：南通大学——地理信息171——刘大炜',
				subtextStyle:{
					fontSize:15,	
				},
        		left: 'center'
      		},
		//信息
		series: [
                {
                  name: '确诊数',
                  type: 'map',
                  mapType:mapName,//省份数据的maptype类型要和名称一一对应
				showLegendSymbol: false,
                  roam: false,
					label: {
                    show: true,
                    color: 'rgb(249, 249, 249)'
                  },
                  data: mapArr,
                }
              ],
		//设置鼠标悬停的样式
		tooltip:{
			formatter:function(Param){ 
				//console.log(Param,'formatter');
				console.log(Param.name);
				return '地区：'+(Param.name||"暂无数据")+'<br/>确诊：'+(Param.value || 0)+'人<br/>治愈：'+(((Param.data) && (Param.data.curedCount ))|| 0)+'人<br/>死亡：'+((Param.data) &&(Param.data.deadCount) || 0)+'人<br/>'
				}
			},
		//设置分级标准
		visualMap: {
					type: 'piecewise',
					 pieces: [
					  { min: 10000,label: '确诊大于等于10000人', color: 'rgb(72,15,16)' },
					  { min: 1000, max: 9999, label: '确诊1000-9999人', color: 'rgb(150,57,40)' },
					  { min: 100, max: 999, label: '确诊100-999人', color: 'rgb(213,99,85)' },
					  { min: 10, max: 99, label: '确诊10-99人', color: 'rgb(233,161,136)' },
					  { min: 1, max: 9, label: '确诊1-9人', color: 'rgb(250,235,210)' },
					],
					left:'5%',
					bottom:'5%',
					textStyle:{
						color:'#ccc'
					}
				},
		})
}

  </script>
</body>
</html>
```
最终效果如下：

{% asset_img  echarts第三次下钻测试.gif %}

## 3 实验心得

1. 在本次实验后对echarts有了的一个初步了解。
2. 在最终的下钻测试中，代码可读性较差，模块化不强。
3. 在数据的引用上没有做到封装，每次都要重复引用，效率较差。
4. 本次实验以完成地图下钻，显示疫情实时分布信息为目的，暂时未考虑性能问题。

谢谢观看！ 写于： 2020年3月10日21:39:03

























