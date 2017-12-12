---
layout: blogModel
title: 我的第一个插件：canvas圆形进度条
---
基于jquery的canvas绘图插件。
<!-- more -->
### 示例

<canvas width="150" height="150" id="myCanvas_1"></canvas>
<canvas width="200" height="200" id="myCanvas_2"></canvas>


使用如下：
```
<canvas width="200" height="200" id="myCanvas_1"></canvas>
<canvas width="300" height="300" id="myCanvas_2"></canvas>
$('#myCanvas_1').drawJDT();
$('#myCanvas_2').drawJDT({
	degree: 60,
	lineWidth: 20,
	lineColor: "red",
	anticlockwise: true,
	rotateDegree: -90
});
```

### API
（以下均采用默认值）  

degree: 10,						//进度条百分比  
refreshFrequency: 20,   		//刷新频率（ms）  
perDegree: 1,   				//每次刷新增加的百分比  
lineWidth: 10,  				//进度条宽度  
bgColor: "agba(0,255,255,0)",	//进度条背景色  
lineColor: "#fd3",				//进度条颜色  
centerX: canvas.width/2,		//圆心坐标x  
centerY: canvas.height/2,		//圆心坐标y  
radius: canvas.width/2-20,		//圆半径  
anticlockwise: false,			//顺时针(false)、逆时针（true）  
rotateDegree: 90				//进度条旋转角度  

  
<script type="text/javascript">

		(function($){
			$.fn.drawJDT = function(options){
				var canvas = $(this)[0],
					context = $(this)[0].getContext("2d");

				var defaults = {
					degree: 10,
					refreshFrequency: 20,
					perDegree: 1,
					lineWidth: 10,
					bgColor: "agba(0,255,255,0)",
					lineColor: "#fd3",
					centerX: canvas.width/2,
					centerY: canvas.height/2,
					radius: canvas.width/2-20,
					anticlockwise: false,
					rotateDegree: 90
				}
				var settings = $.extend(defaults,options);
				
				var nowDegree = 0;
				if(settings.anticlockwise){
					nowDegree = 100;
					settings.perDegree = -1*settings.perDegree;
					settings.degree = 100 - settings.degree;
				}

				CanvasRenderingContext2D.prototype.jindutiao = function(degree,context){
					drawBackRoll();

					var timer = setInterval(function(){
						update(degree);
					},settings.refreshFrequency);


					function drawBackRoll(){
						context.beginPath();
						context.strokeStyle = settings.bgColor;
						context.lineWidth = settings.lineWidth;
						context.arc(settings.centerX,settings.centerY,settings.radius,0,2*Math.PI,anticlockwise=settings.anticlockwise);
						context.stroke();
					}
					function update(degree){
						
						function updateContent(){
							context.clearRect(0,0,canvas.width,canvas.height);

							drawBackRoll();

							context.beginPath();
							context.save();

							context.translate(settings.centerX,settings.centerY);
							context.lineWidth = settings.lineWidth + 0.5;
							context.strokeStyle = settings.lineColor; 
							context.rotate(-settings.rotateDegree*Math.PI/180); 
							context.translate(-settings.centerX,-settings.centerY);

							context.arc(settings.centerX,settings.centerY,settings.radius,0,nowDegree/100*2*Math.PI,anticlockwise=settings.anticlockwise);
							context.stroke();
							context.restore();
							nowDegree += settings.perDegree;
						}

						if(!settings.anticlockwise){
							if(nowDegree <= degree){
								updateContent();
							}else{
								nowDegree = degree;
								clearInterval(timer);
							}
						}else{
							if(nowDegree >= degree){
								updateContent();
							}else{
								nowDegree = degree;
								clearInterval(timer);
							}
						}
					}
				}
				context.jindutiao(settings.degree,context);
			}
		})(jQuery);

		$('#myCanvas_1').drawJDT();
		$('#myCanvas_2').drawJDT({
			degree: 60,
			lineWidth: 20,
			lineColor: "red",
			anticlockwise: true,
			rotateDegree: -90
		});

	</script>