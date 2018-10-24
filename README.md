# DisMiC
imakewemake tshs

<!DOCTYPE html>
<!--2017/07/14 Andy Song - A remoted joystick with GoGoCam for CPF web app -->
<head>
	<title>Remote Control Car</title>
	<link rel="stylesheet" type="text/css" href="css/style.css">
	<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>
</head>
<body>

	<center>
		<div id="content">
			<img src="images/controller.png" id="controller">
			<img src="images/left_right_control.png" id="left_right_control"/>
			<img src="images/left.png" id="left_control"><!--左-->
			<img src="images/right.png" id="right_control"><!--右-->
			<img src="images/top_bottom_control.png" id="top_bottom_control"/>
			<img src="images/top.png" id="top_control"><!--上-->
			<img src="images/bottom.png" id="bottom_control"><!--下-->
			<div id="lightValue">100</div>

			<img src="images/right_button.png" id="right_button"/><!--停-->
			<img src="images/left_button.png" id="left_button"/><!--加速-->
		</div>

	</center>

</body>

<script>
	setup();
	var d12, d13;
	var d10 = 0, d11 = 0;
	var going = document.getElementById("top_control");
	var back = document.getElementById("bottom_control");
	var left = document.getElementById("left_control");
	var right = document.getElementById("right_control");
	var stop1 = document.getElementById("left_button");
	var stop2 = document.getElementById("right_button");
	var lightValue = 0;
	var speed = 5;
	
	//顯示光數值
	function loop() {
		if(cpf){
			lightValue = cpf.get("light sensor");
			document.getElementById("lightValue").innerHTML = lightValue;
		}
		setTimeout("loop()", 1000);
	}
	
	loop();

	function carRun(){
		cpf.request('["digitalWrite", 12, ' + d12 + '],["digitalWrite", 13, ' + d13 + '],["analogWrite", 10, ' + d10 + '],["analogWrite", 11, ' + d11 + ']');
	}


	function stop(){
		d10 = 0;
		d11 = 0;
		cpf.request('["analogWrite", 10, ' + d10 + '],["analogWrite", 11, ' + d11 + ']');
	}

	//停止
	stop1.addEventListener('touchstart', function(event) {
		stop();		
	});
	stop2.addEventListener('touchstart', function(event) {
		stop();		
	});

	// 後退
	back.addEventListener('touchstart', function(event) {
		d12 = 0;
		d13 = 1;
		d10 = 255;
		d11 = 255;
		carRun();
	});
	back.addEventListener('touchend', function(event) {
		stop();		
	});
	// 前進
	going.addEventListener('touchstart', function(event) {
		d12 = 1;
		d13 = 0;
		d10 = 255;
		d11 = 255;
		carRun();
	});
	going.addEventListener('touchend', function(event) {
		stop();		
	});
	//左轉
	left.addEventListener('touchstart', function(event) {
		if(d10==0 && d11==0){
			d12 = 1;
			d13 = 1;
			d10 = 255;
			d11 = 255;
		}else{
			d11 = 125;
		}
		carRun();
	});
	left.addEventListener('touchend', function(event) {
		if(d10==255 && d11==125){
			d11 = 255;
			carRun();
		}else{
			stop()
		}
	});
	//右轉
	right.addEventListener('touchstart', function(event) {
		if(d10==0 && d11==0){
			d12 = 0;
			d13 = 0;
			d10 = 255;
			d11 = 255;
		}else{
			d10 = 125;
		}
		carRun();
	});	
	right.addEventListener('touchend', function(event) {
		if(d10==125 && d11==255){
			d10 = 255;
			carRun();
		}else{
			stop()
		}	
	});

	function setup(){
		//d12,d13前後
		//d10,d11速度
		if(cpf){
			cpf.setPinMode('["resetPin"],["setPinMode", "analog", 0, "INPUT"],["setPinMode", "digital", 10, "PWM"],["setPinMode", "digital", 11,"PWM"],["setPinMode", "digital", 12,"OUTPUT"],["setPinMode", "digital", 13,"OUTPUT"]');
		}
	}
</script>

</html>
