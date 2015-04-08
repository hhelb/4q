
window.addEventListener('load', function(e) {


	// Level
	var level = 1 ;
	var GAME_TIMER = 120 ;

	// quarts colors
	var QUART_COLOR_BASIC = "#FFFFFF"
	var QUART_COLOR_ACCEPTED = "#F79F81"
	var QUART_COLOR_MULTI_ACCEPTED = "#FA8258"


	// game size
    var gameWidth = 480;
    var gameHeight = 860;

    // quart
    var centerX = gameWidth / 2
    var centerY = gameHeight /2 + 100
    var quartSpace = 80
	var rotationAngle = 10
	var nbAcceptedQuart = 0

	// Ball
	var operationBallX =(gameWidth / 2)
	var operationBallY = 0
	var BALL_TIMER =3 //  seconds
	var lastOp = 0

	// Operation Limit
	var LIMIT_OP_PLUS = 10 ;
	var LIMIT_OP_MINUS = 4 ;
	var LIMIT_OP_DIV = 4 ;
	var LIMIT_OP_MUL = 2 ;

	// rotation button position
	var rotationBtnLeft = gameWidth * 0.20 ;
	var rotationBtnRight = gameWidth  * 0.80 ;
	var RotationBtnY = gameHeight - 150 ;

    //flag
    var ballDestroy = false ;

	// quart rotation
	var QUART_ROTATION_SPEED = 8 ;
	var isOnrotation = false ;
	var dragRotation = 0 ;


    var Q = window.Q = Quintus().include("Audio, Sprites, Scenes, 2D, Input, Touch, UI,  Anim")

	Q.setup({ width: gameWidth, height: gameHeight , scaleToFit: true }).touch(Q.SPRITE_ALL);

	function goHTML5Audio()
	{
    Q.assets = {};
    Q.audio.enableHTML5Sound();
	}


	// collision types
    Q.SPRITE_QUART = 2;
    Q.SPRITE_BALL = 4;
	Q.SPRITE_PIPE = 8 ;
	Q.SPRITE_OTHER = 16 ;

	Q.Sprite.extend('Operation',
	{
			init: function(props)
			{
				var op = this.randomOperation() ;
				var opColor = this.getColor(op) ;
				this._super(Q._extend(props,
				{
					w: 40, h: 40, x: operationBallX , y: operationBallY , color : opColor , type : Q.SPRITE_BALL,  operationType: op ,
					operationValue: this.randomValue(1,3) , gravity: 0.98, collisionMask: Q.SPRITE_QUART | Q.SPRITE_BALL | Q.SPRITE_PIPE | Q.SPRITE_OTHER,
					opacity : 0.5, isTheNextOne : false, ballTimer : BALL_TIMER
				}));

				this.add('2d');
				this.on("hit.sprite",function(collision)
				{
					if( (collision.obj.p.type == Q.SPRITE_PIPE ) && ! this.p.isTheNextOne )
					{
					// the ball collide with the pipe's gate, the ball should fall after a timer
					this.p.isTheNextOne = true ;
					}
				});

				this.on('step', function(dt)
				{
					if (this.p.isTheNextOne)
					{
						this.p.ballTimer-= dt
					}

					if (this.p.ballTimer <= 0)
					{
						this.p.collisionMask = Q.SPRITE_QUART
					}

				});
		   },

			draw: function(ctx)
			{

				ctx.fillStyle = this.p.color ;
				ctx.beginPath();
				ctx.arc(-this.p.cx +20, -this.p.cy +20, this.p.w/2,0,Math.PI*2);
				ctx.fill();
			},

			randomValue: function(min, max)
			{
				return Math.floor(Math.random() * (max - min 	+ 1)) + min;
			},

			randomOperation: function()
			{
				var operation = getRandom();
				switch (operation)
				{
				case 1 :
					return '+' ;
					break;

				case 2 :
					return '-' ;
					break;

				case 3 :
					return '×' ;
					break;

				case 4 :
					return '÷' ;
					break;

				default:
				break;
				}
			},

			getColor : function (op)
			{

				switch (op)
				{
				case '+' :
					return "blue" ;
					break;

				case '-' :

					return "orange" ;
					break;

				case '×' :

					return "green" ;
					break;

				case '÷' :

					return "grey" ;
					break;

				default:
					break;
				}
		   }

	});

	Q.Sprite.extend('QuartContainer',

	{
		init: function(props)
		{

			this._super(props,
			{
				w : gameWidth /2 + 70 , h : gameWidth /2 + 70
			});

		},

		rotate : function (myAngle)
		{
			this.p.angle = this.p.angle + myAngle ;
		}
	});


	Q.Sprite.extend('RotationBtn',
	{
		init: function(props)
		{
			this._super(props,
			{
				w : 100 , h : 100 , y : RotationBtnY, rotationDirectionCode : 0, asset : "Arrow", scale : 1

			});

			this.on("touch");
			this.on("touchEnd");
		},

		touch: function(touch)
		{

			isOnrotation = true ;
			dragRotation = (this.p.rotationDirectionCode) * ( QUART_ROTATION_SPEED ) ;
		},

		touchEnd : function( touch)
		{
			isOnrotation = false ;
		}
	});


	Q.Sprite.extend('Quart',
	{
		init: function(props)
		{
			this._super(Q._extend(props,
			{
				value: 0,
				points: [
					[ 150 ,150],
					[ 0 ,150],
					[ 150, 0]
						],
				color : QUART_COLOR_BASIC ,
				type : Q.SPRITE_QUART,
				collisionMask : Q.SPRITE_BALL,
				lockBallCollision : false,
				rotationDirectionCode : 0 // code for lock quart rotation -1 left +1 right 0 no rotation
			}));

			this.on("hit.sprite",function(collision )
			{
				if( (collision.obj.isA("Operation")) &&  !this.p.lockBallCollision)
				{
					this.p.lockBallCollision = true ;
					switch (collision.obj.p.operationType)
					{
					case '+':
						this.p.value =this.p.value + collision.obj.p.operationValue ;
						collision.obj.destroy() ;
						ballDestroy = true;
					break;
					case '-':
						this.p.value =this.p.value - collision.obj.p.operationValue ;
						collision.obj.destroy() ;
						ballDestroy = true;
					break;
					case '×':
						this.p.value =this.p.value * collision.obj.p.operationValue ;
						collision.obj.destroy() ;
						ballDestroy = true;
					break;
					case '÷':
						this.p.value = Math.floor(this.p.value / collision.obj.p.operationValue ) ;
						collision.obj.destroy() ;
						ballDestroy = true;
					break;
					default:
					break;
					}
					// play hit sound
					// Q.audio.play("HitSound");

					if (this.p.value <= 0) {this.p.value = 0 ;}
					if (this.p.value >= 999999) {this.p.value = 999999 ;}

				}
			});


		},

		draw: function(ctx)
		{
			var x = 150;
			var y = 150;
			var radius = 150;
			var startAngle = Math.PI;
			var endAngle = Math.PI*1.5;
			var antiClockwise = false;

			ctx.beginPath();
			ctx.arc(x, y, radius, startAngle, endAngle, antiClockwise);
			ctx.lineTo(150, 150);
			ctx.closePath();
			ctx.fillStyle = this.color ;
			ctx.fill();

		},

		changeQuarterColor : function ()
		{
			this.draw = function(ctx)
			{
				var x = 150;
				var y = 150;
				var radius = 150;
				var startAngle = Math.PI;
				var endAngle = Math.PI*1.5;
				var antiClockwise = false;

				ctx.beginPath();
				ctx.arc(x, y, radius, startAngle, endAngle, antiClockwise);
				ctx.lineTo(150, 150);
				ctx.closePath();
				/* ctx.globalAlpha = 0.5; */
				ctx.fillStyle = this.p.color ;
				ctx.fill();

			}
		}
	});


    // Create a simple scene that adds two shapes on the page
    Q.scene("start", function(stage)
	{

		

		var quart1 = new Q.Quart({x:  2* quartSpace  , y: 2 * quartSpace ,
            angle: 180, gravity: 0 });
        var quart2 = new Q.Quart({x: - 2 * quartSpace  , y: 2 * quartSpace  ,
            angle: 270, gravity: 0  });
        var quart3 = new Q.Quart({x: - 2 * quartSpace  , y:- 2 * quartSpace  ,
            angle: 0,  gravity: 0  });
        var quart4 = new Q.Quart({x: 2 * quartSpace , y: - 2 * quartSpace ,
            angle: 90,  gravity: 0  });

		var quartContainer = new Q.QuartContainer({x: centerX , y: centerY });

		stage.insert(quartContainer);
        stage.insert(quart1,quartContainer);
        stage.insert(quart2,quartContainer);
        stage.insert(quart3,quartContainer);
        stage.insert(quart4,quartContainer);

		stage.insert(new Q.RotationBtn({ rotationDirectionCode : 1, x : rotationBtnRight, flip :"x" })) ;
		stage.insert(new Q.RotationBtn({ rotationDirectionCode : -1, x : rotationBtnLeft })) ;


		// audio
		// Q.audio.play("AmbSound", { loop: true});


		// timer
		var timer = GAME_TIMER ;
		var timerMin ;
		var timerSec ;

        stage.on('step', function(dt)
		{

			// update timer
			timer -= dt ;
			timerMin = parseInt( Math.floor(timer) / 60 ) % 60;
			timerSec = Math.floor(timer) % 60;
			labelTimer.p.label= "TIME \n"+timerMin.toString()+" : "+timerSec.toString() ;

			// loosing
			 if (timer <= 0 )
			 {
				ballDestroy = false ;
				Q.clearStages();
				Q.stageScene("endGame",1, { label: "You Lose!"});
			 }

			// if the ball is out
			if (ballDestroy )

			{

				//unlock collisions on quarts
				quart1.p.lockBallCollision = false ;
				quart2.p.lockBallCollision = false ;
				quart3.p.lockBallCollision = false ;
				quart4.p.lockBallCollision = false ;


				// testing game end

				if (winCondition(quart1, quart2, quart3, quart4))
				{
					 level++ ;
					 ballDestroy = false ;
					 Q.clearStages() ;
					 Q.stageScene("endGame",1, { label: "You Won!"});
				}

				insertBall(stage, new Q.Operation({ }), new Q.UI.Text({color: "white", x: 0, y: -15})) ;
                ballDestroy = false ;

				// changing quart color
				var localNbacceptedQuart = colorQuarts(quart1,quart2,quart3,quart4) ;

				if ((localNbacceptedQuart > nbAcceptedQuart ) && localNbacceptedQuart != 0 )
				{
					notify(notification, getEncouragement()) ;
					timer+= 30 ;
				}

				nbAcceptedQuart = localNbacceptedQuart ;
		   }

		if (isOnrotation)
		{
			quartContainer.rotate(dragRotation) ;
			// isOnrotation = false ;
		}

			// update value

			labelQ1.p.label = quart1.p.value.toString() ;
			labelQ2.p.label = quart2.p.value.toString() ;
			labelQ3.p.label = quart3.p.value.toString() ;
			labelQ4.p.label = quart4.p.value.toString() ;

	  });

			// level label
			labelLevel = stage.insert(new Q.UI.Text({label: "LEVEL \n"+level.toString(), color: "white", x: 80, y: 20}));
			labelTimer =new Q.UI.Text({label: "TIME \n"+timer.toString(), color: "white", x: gameWidth - 80, y: 20});
			stage.insert(labelTimer) ;


			// quarts labels
			var labelQ1 = new Q.UI.Text({label: quart1.p.value.toString(), color: "#590805", x: quartSpace , y: quartSpace , angle : 315}) ;
			stage.insert(labelQ1, quart1);
			var labelQ2 = new Q.UI.Text({label: quart2.p.value.toString(), color: "#590805", x: quartSpace, y: quartSpace, angle : 315}) ;
			stage.insert(labelQ2, quart2);
			var labelQ3 = new Q.UI.Text({label: quart3.p.value.toString(), color: "#590805", x: quartSpace, y: quartSpace, angle : 315}) ;
			stage.insert(labelQ3, quart3);
			var labelQ4 = new Q.UI.Text({label: quart4.p.value.toString(), color: "#590805", x: quartSpace, y: quartSpace, angle : 315}) ;
			stage.insert(labelQ4, quart4);


			// Draw the pipe
			stage.insert(new Q.Sprite({w: 15, h: 270, x: (gameWidth / 2) - 30, y: 15, color: "white" , type : Q.SPRITE_OTHER,
						collisionMask : Q.SPRITE_BALL}));
			stage.insert(new Q.Sprite({w: 15, h: 270, x: (gameWidth / 2) + 30, y: 15, color: "white" ,type : Q.SPRITE_OTHER,
						collisionMask : Q.SPRITE_BALL}));
			stage.insert(new Q.Sprite({w: 100, h: 20, x: (gameWidth / 2), y: 160, color: "white",type : Q.SPRITE_PIPE,
						collisionMask : Q.SPRITE_BALL}));
			stage.insert(new Q.Sprite({w: 160, h: 20, x: (gameWidth / 2), y: 180, color: "white",type : Q.SPRITE_PIPE,
						collisionMask : Q.SPRITE_BALL}));


			// Notification
			var notification = new Q.UI.Text(
			{
				label: "GO !",
				color: "white",
				size : 40,
				x: gameWidth/2,
				y: gameHeight/2,
				opacity : 1
    		}) ;

			notification.add("tween");
			notification.animate({ x: gameWidth/2, y: gameHeight/3},0.4, Q.Easing.Quadratic.InOut );
			notification.step = function(dt)
			{
				if ( this.p.opacity> 0) this.p.opacity -= dt/3 ;
				if ( this.p.opacity <= 0) this.p.opacity = 0 ;
			}

			stage.insert(notification);
			// balls

			// first ball - used to as indication for collision test
			var operation = stage.insert(new Q.Operation({ }));
			var labelOperation = stage.insert(new Q.UI.Text({label: operation.p.operationType+operation.p.operationValue.toString(), color: "white", x: 0,
				y: -15}), operation);

			// other balls
			insertBall(stage , new Q.Operation({ }) ,  new Q.UI.Text({color: "white", x: 0, y: -15}) ) ;
			insertBall(stage , new Q.Operation({ }) ,  new Q.UI.Text({color: "white", x: 0, y: -15}) ) ;


			// Initial rotate 45°
			quartContainer.rotate(45) ;

			// initial color
			nbAcceptedQuart = colorQuarts(quart1,quart2,quart3,quart4) ;

			// Bind the basic inputs to different behaviors of quart
			Q.input.on('left', stage, function(e)
			{
				quartContainer.rotate(-rotationAngle) ;
			});

			Q.input.on('right', stage, function(e)
			{
				quartContainer.rotate(rotationAngle) ;
		    });

	});

	function insertBall(stage , op , label)
	{
		var operation = stage.insert(op);
		label.p.label = op.p.operationType+op.p.operationValue.toString() ;
        var labelOperation = stage.insert(label,operation);

	}

	function notify (notification, value)
	{
		notification.p.label = value ;
		notification.p.opacity = 1 ;

	}

	function colorQuarts(q1,q2,q3,q4)
	{
		var candidates = [ q1.p.value , q2.p.value , q3.p.value , q4.p.value ] ;
		var nbMuttiAccepted = 0;
		if (chooseQuartColor(q1,candidates) ) {nbMuttiAccepted++} ;
		if (chooseQuartColor(q2,candidates) ) {nbMuttiAccepted++} ;
		if (chooseQuartColor(q3,candidates) ) {nbMuttiAccepted++} ;
		if (chooseQuartColor(q4,candidates) ) {nbMuttiAccepted++} ;
		return nbMuttiAccepted ;
	}

	//random with probabilities
	function getRandom()
	{
		do
		{
			var op=Math.random();
			if(op <= 0.50) { op = 1 ; break}
			else if(op <= 0.70) { op = 2}
			else if(op <=0.85) { op = 3}
			else if(op <=1) { op = 4}

		} while (op == lastOp)

		lastOp = op ;
		return op ;
	}

	function winCondition(q1,q2,q3,q4)
	{
		if ( ( q1.p.value == q3.p.value ) &&  ( q2.p.value == q4.p.value ) && ( q1.p.value == q2.p.value )
			&& ( q1.p.value > 0 ) && ((q1.p.value % level) == 0) ) { return true} else{ return false }
	}

	function getEncouragement()
	{
		var op= Math.floor(Math.random() * (4 )) + 1
		if(op == 1) return "Nice !";
		if(op == 2) return "Great !";
		if(op == 3) return  "Well done !";
		if(op == 4) return "Continue !";
	}

	function chooseQuartColor(q,values)
	{
		var isMultiAccepted = false;
		var nbElement = 0 ;
		for	(index = 0; index < values.length; index++)
		{
			if ( q.p.value == values[index] ) { nbElement++ ; }
		}
		if ( ((q.p.value % level) == 0) && ( nbElement > 1) && (q.p.value != 0)){ q.p.color = QUART_COLOR_MULTI_ACCEPTED, isMultiAccepted = true }
		else if ( ((q.p.value % level) == 0 ) && (q.p.value != 0)) { q.p.color = QUART_COLOR_ACCEPTED }
		else {q.p.color = QUART_COLOR_BASIC} ;
		q.changeQuarterColor() ;
		return isMultiAccepted;
	}


	Q.scene('endGame',function(stage)
	{


  		var nextButton = stage.insert(new Q.UI.Button({fill: "#FA8258", border: 5, shadow: 10,  x: gameWidth/2, y: 400, label:  "  NEXT  " }))
  		var shareButton = stage.insert(new Q.UI.Button({fill: "#FA8258", border: 5, shadow: 10, x: gameWidth/2, y : nextButton.p.y + 70, label: "  SHARE " }))
  		var label = stage.insert(new Q.UI.Text({ x : gameWidth/2 , y: 200 , label: stage.options.label, color: "white",
				size : 70 }));
  		nextButton.on("click",function()
		{
    		Q.clearStages();
    		Q.stageScene('start');
  		});
		shareButton.on("click",function()
		{
			window.open("http://www.facebook.com/4qgame");

  		});


  		container.fit(20);
	});



	Q.scene('beginGame',function(stage)
	{


  		var logoImg = stage.insert(new Q.UI.Button({ asset: 'Logo' , x : gameWidth/2 , y: gameHeight/3 , scale : 0.40 }))
  		var startBtn = stage.insert(new Q.UI.Button({ fill: "#FA8258", border: 5, shadow: 10, x : gameWidth/2 , y: gameHeight/1.7, size : 100, label: "       START       " }))
  		var htpBtn = stage.insert(new Q.UI.Button({ fill: "#FA8258", border: 5, shadow: 10, x : gameWidth/2 , y: startBtn.p.y + 70, size : 100, label: "HOW TO PLAY" }))
		var shareBtn = stage.insert(new Q.UI.Button({ fill: "#FA8258", border: 5, shadow: 10, x : gameWidth/2 , y: htpBtn.p.y + 70, size : 100, label: "       SHARE       " }))

  		startBtn.on("click",function()
		{
    		Q.clearStages();
    		Q.stageScene('start');
  		});

		htpBtn.on("click",function()
		{
    		Q.clearStages();
			Q.stageScene('howToPlay');
  		});

		shareBtn.on("click",function()
		{
			window.open("http://www.facebook.com/4qgame");
  		});


	});

	Q.scene('howToPlay',function(stage)
	{
	  var container = stage.insert(new Q.UI.Container(
		  {
			x: Q.width/2, y: Q.height/2, fill: "rgba(0,0,0,0.5)"
		  }));
	  var htpImg = stage.insert(new Q.UI.Button({ asset: 'Htp' , x : gameWidth/2 , y: gameHeight/2 - 80 , scale : 0.35 }))
	  var backBtn = stage.insert(new Q.UI.Button({ fill: "#FA8258", border: 5, shadow: 10, x : gameWidth/2 , y: gameHeight - 120 , size : 100 ,
												   label: "BACK" }))
	  backBtn.on("click",function()
	  {
		Q.clearStages();
		Q.stageScene('beginGame');
	  });

	  container.fit(20);
	});



	goHTML5Audio() ;
    Q.load(
		{
		  "Logo" : "logo.png",
          "AmbSound": "amb.mp3",
          "HitSound": "hit.mp3",
		  "Htp": "htp.jpg",
		  "Arrow": "arrow.png"
        }, function()
		{

        // Start the show
        Q.stageScene("beginGame");

        // Turn visual debugging on to see the
        // bounding boxes and collision shapes
		// Q.debug = true;

        // Turn on default keyboard controls
        Q.input.keyboardControls();
    });

});
	
