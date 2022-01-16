module fighter(
output reg[7:0] DATA_R,
output reg[7:0] DATA_G,
output reg[7:0] DATA_B,
output reg[2:0] COMM,
output reg enable,
output reg[3:0] life,
output reg bullet,
output reg[6:0] sevenScreen,
output reg [1:0] screenCOM,
output audio,
input CLK, LButton, RButton, LaserBeamButton, ResetButton,NormalButton,EasyButton,HardButton, BigLaserBeamButton, music_change);

	bit [2:0]	x_count;
	reg [2:0]	x_jet;
	reg [2:0]	x_ball;
	reg [2:0]	x_badjet;
	reg [2:0]	x_badjet2;
	reg [3:0]	score_one;
	reg [3:0]	score_ten;
	reg[23:0]	counter8Hz;
   reg[23:0]	counter6MHz;
   reg[13:0]	count;
   reg[13:0]	origin;
   reg[4:0] 	j;
   reg[7:0] 	len1;
	reg[7:0] 	len2;
   reg			audiof;
   reg			clk_6MHz;
   reg			clk_8Hz;
	reg			difficult;
	assign		audio = audiof; // 控制開關
 

	divfreq1kHz F0(CLK, CLK_div1k); //畫面的HZ
	
	Hard Q1(CLK, CLK_Hard); //按鈕的HZ
	Normal F1(CLK, CLK_Normal); //按鈕的HZ
	Easy W1(CLK, CLK_Easy); //按鈕的HZ

	initial
		begin
			x_count = 3'b000;
			DATA_R = 8'b11111111;
			DATA_G = 8'b11111111;
			DATA_B = 8'b11111111;
			COMM = 3'b000;
			enable = 1;
			x_jet = 3'b011;
			x_badjet = 3;
			score_one = 4'b0000;
			score_ten = 4'b0000;
			life = 4'b1111;
		end
	integer ballCD_down;
	integer laserCD;
	integer LaserBeam;
	integer BigLaserBeam;
	integer badjetCD;
	integer ballCD;
	integer y_ball = 8;
	integer y_badjet = 8;
	integer BadNumRand = 0;
	integer IsTwoBad = 0;
	integer BadNum = 1;
	integer x_ballrandom = 5;
	integer x_badrandom = 7;
	integer scorePoint = 0;
	integer BadBreak = 0;
	integer BadBreak2 = 0;
	integer score_enable = 0;
	integer isate = 0;
	integer isgone = 0;
	integer isCrush = 0;
	integer isCrush2 = 0;
	integer lifeLeft = 0;
	integer minusLife = 0;
	integer alphaCount = 0;
	integer alphaCD = 0;
	integer score = 0;

	
	
	//過關and失敗畫面
	logic [7:0] alpha[7:0] = 
	  '{8'b11111111,
		 8'b11111111,
		 8'b11111111,
		 8'b11111111,
		 8'b11111111,
		 8'b11111111,
		 8'b11111111,
		 8'b11111111};
	parameter logic [7:0] CLEAR[7:0] =
	  '{8'b11111111,
		 8'b11111111,
		 8'b11111111,
		 8'b11111111,
		 8'b11111111,
		 8'b11111111,
		 8'b11111111,
		 8'b11111111};
	parameter logic [7:0] V[7:0] =
	  '{8'b11111111,
		 8'b00000011,
		 8'b11111101,
		 8'b11111110,
		 8'b11111110,
		 8'b11111101,
		 8'b00000011,
		 8'b11111111};
	parameter logic [7:0] I[7:0] =
	  '{8'b01111110,
		 8'b01111110,
		 8'b01111110,
		 8'b00000000,
		 8'b01111110,
		 8'b01111110,
		 8'b01111110,
		 8'b01111110};
	parameter logic [7:0] C[7:0] =
	  '{8'b11111111,
		 8'b10111101,
		 8'b01111110,
		 8'b01111110,
		 8'b01111110,
		 8'b01111110,
		 8'b10111101,
		 8'b11000011};
	parameter logic [7:0] T[7:0] =
	  '{8'b01111111,
		 8'b01111111,
		 8'b01111111,
		 8'b00000000,
		 8'b01111111,
		 8'b01111111,
		 8'b01111111,
		 8'b01111111};
	parameter logic [7:0] O[7:0] =
	  '{8'b11000011,
		 8'b10111101,
		 8'b01111110,
		 8'b01111110,
		 8'b01111110,
		 8'b01111110,
		 8'b10111101,
		 8'b11000011};
	parameter logic [7:0] R[7:0] =
	  '{8'b11111111,
		 8'b11111110,
		 8'b10011101,
		 8'b01101011,
		 8'b01100111,
		 8'b01101111,
		 8'b00000000,
		 8'b11111111};
	parameter logic [7:0] Y[7:0] =
	  '{8'b10111111,
		 8'b11011111,
		 8'b11101111,
		 8'b11110000,
		 8'b11101111,
		 8'b11011111,
		 8'b10111111,
		 8'b01111111};
		 
	parameter logic [7:0] D[7:0] =
	  '{8'b11111111,
		 8'b11000011,
		 8'b10111101,
		 8'b01111110,
		 8'b01111110,
		 8'b01111110,
		 8'b00000000,
		 8'b11111111};
	parameter logic [7:0] E[7:0] =
	  '{8'b11111111,
		 8'b01101110,
		 8'b01101110,
		 8'b01101110,
		 8'b01101110,
		 8'b01101110,
		 8'b00000000,
		 8'b11111111};
	parameter logic [7:0] F[7:0] =
	  '{8'b11111111,
		 8'b01101111,
		 8'b01101111,
		 8'b01101111,
		 8'b01101111,
		 8'b01101111,
		 8'b00000000,
		 8'b11111111};
	parameter logic [7:0] A[7:0] =
	  '{8'b11111111,
		 8'b11000000,
		 8'b10110111,
		 8'b01110111,
		 8'b01110111,
		 8'b10110111,
		 8'b11000000,
		 8'b11111111};


		 
	always@(posedge CLK)
		begin
			if(HardButton)
				difficult = CLK_Hard;
			else if(NormalButton)
				difficult = CLK_Normal;
			else if(EasyButton)
				difficult = CLK_Easy;
		end
	always @(posedge difficult) //操作
		begin 
			
			if(ResetButton)
				begin
					life = 4'b1111;
					score_one = 4'b0000;
					score_ten = 4'b0000;
					x_badjet = 3;
					y_badjet = 8;
					x_ball = 5;
					y_ball = 8;
					BadNumRand = 0;
					IsTwoBad = 0;
					BadNum = 1;
					//x_ballrandom = 5;
					//x_badrandom = 7;
					scorePoint = 0;
					BadBreak = 0;
					BadBreak2 = 0;
					isCrush = 0;
					isCrush2 = 0;
					lifeLeft = 0;
					minusLife = 0;
					bullet = 0;
					ballCD_down = 0;
					score = 0;
				end

			//左右方向鍵
			if(LButton && x_jet != 1)
				x_jet <= x_jet - 1;
			else if(RButton && x_jet != 6)
				x_jet <= x_jet + 1;
			
			//Laser Cool Down
			if(laserCD < 25)
				begin
					LaserBeam = 0;
					laserCD = laserCD + 1;
				end
			else if (LaserBeamButton == 0 && laserCD >= 3)
				begin
					laserCD = laserCD + 1;
				end
			else 
				begin
					laserCD = 0;
					if(LaserBeamButton)
						LaserBeam = 1;					
				end
			//Big Laser
			if(!isate)
				begin
					BigLaserBeam = 0;
				end
			else if(BigLaserBeamButton && isate)
				begin
					BigLaserBeam = 1;
					isate = 0;
					bullet = 0;
				end
	
			BadNumRand = BadNumRand + 1;
			//Bad Jet fall
			if(badjetCD < 2)
				begin
					badjetCD = badjetCD + 1;
				end			
			else
				begin
					badjetCD = 0;
					y_badjet = y_badjet - 1;
					if(y_badjet < 0)
						begin
							//隨機重設飛機的位置 and 飛機數量(1 or 2)
							y_badjet = 8;
							ballCD = ballCD + 1;
							x_badrandom = x_badrandom * (x_badrandom - 3) - 5;
							if(x_badrandom > 2000)
								x_badrandom = x_badrandom - 1990;
							IsTwoBad = BadNumRand % 2;
							x_badjet = (x_badrandom % 6) + 1;
							//隨機一次是否有兩個敵人
							if(IsTwoBad && (x_badjet == 1 || x_badjet == 2 || x_badjet == 5 || x_badjet == 6))
								begin
									BadNum = 2;
									x_badjet2 = (BadNumRand % 3) % 2 ;
									case (x_badjet)
										1 : x_badjet2 = x_badjet2 + 5;
										2 : x_badjet2 = 6;
										5 : x_badjet2 = 1;
										6 : x_badjet2 = x_badjet2 + 1;
									endcase
								end
							else
								BadNum = 1;
						end
					//扣血
					if((isCrush || isCrush2) && y_badjet == 0)
						begin
							life[lifeLeft] = 1'b0;
							lifeLeft = lifeLeft + 1;
						end
				end
			//隨機波數生成ball
			if(ballCD > 2 && BadNum == 1)
				begin
					//ball fall
					if(ballCD_down < 2)
						begin
							ballCD_down = ballCD_down + 1;
						end
					else
						begin
							//隨機生成ball的位置
							ballCD_down = 0;
							y_ball = y_ball - 1;
							if(y_ball < 0)
								begin
									y_ball = 8;
									ballCD = 0;			
									x_ballrandom = x_ballrandom * (x_ballrandom - 3) - 5;
									if(x_ballrandom > 2000)
										x_ballrandom = x_ballrandom - 1990;
									x_badjet = x_ballrandom % 8;
								end
							//吃到了,表示可以射擊
							if(isate)
								begin
									bullet = 1;
								end
						end
					//判斷是否吃到
					if(y_ball == 1)
						begin
							if(x_jet == x_ball)
								begin
									isate = 1;
									isgone = 1;
								end
						end
					else if(y_ball == 0)
						begin
							if(x_jet == x_ball || x_jet - 1 == x_ball || x_jet + 1 == x_ball)
								begin
									isate = 1;
									isgone = 1;
								end
						end
					else
						begin
							if(y_ball == 8)
								begin
									isgone = 0;
								end
						end
				end
			//攻擊1成功
			if((x_jet == x_badjet && LaserBeam) || (x_jet - 1 == x_badjet && LaserBeam) || (x_jet + 1 == x_badjet && LaserBeam))
				begin
					BadBreak = 1;
					scorePoint = 1;
					score = score + 1;
				end
			else
				begin
					if(y_badjet == 8)
						BadBreak = 0;
						
				end
			//攻擊2成功
			if((x_jet == x_badjet2 && LaserBeam) || (x_jet - 1 == x_badjet2 && LaserBeam) || (x_jet + 1 == x_badjet2 && LaserBeam))
				begin
						BadBreak2 = 1;
						scorePoint = 1;
						score = score + 1;
				end
			else 
				begin
					if(y_badjet == 8)
						BadBreak2 = 0;
				end
			
			//大雷射攻擊一
			if((x_jet == x_badjet && BigLaserBeam) || (x_jet == x_badjet - 1 && BigLaserBeam) || (x_jet == x_badjet + 1 && BigLaserBeam) || (x_jet == x_badjet - 2 && BigLaserBeam) || (x_jet == x_badjet + 2 && BigLaserBeam))
				begin
					BadBreak = 1;
					scorePoint = scorePoint + 1;
					score = score + 1;
				end
			else
				begin
					if(y_badjet == 8)
						BadBreak = 0;
				end
			//大雷射攻擊二
			if((x_jet == x_badjet2 && BigLaserBeam) || (x_jet == x_badjet2 - 1 && BigLaserBeam) || (x_jet == x_badjet2 + 1 && BigLaserBeam) || (x_jet == x_badjet2 - 2 && BigLaserBeam) || (x_jet == x_badjet2 + 2 && BigLaserBeam))
				begin
					BadBreak2 = 1;
					scorePoint = scorePoint + 1;
					score = score + 1;
				end
			else
				begin
					if(y_badjet == 8)
						BadBreak2 = 0;
				end
			//加分
			if(scorePoint == 1)
				begin
					if(score_one == 4'b1001)
						begin
							score_one = 4'b0000;
							score_ten = score_ten + 1'b1;
							scorePoint = 0;
						end
					else
						score_one = score_one + 1'b1;
						scorePoint = 0;
				end
			else if(scorePoint == 2)
				begin
					if(score_one == 4'b1000)
						begin
							score_one = 4'b0000;
							score_ten = score_ten + 1'b1;
							scorePoint = 0;
						end
					else if(score_one == 4'b1001)
						begin
							score_one = 4'b0000;
							score_ten = score_ten + 2'b10;
							scorePoint = 0;
						end
					else
						score_one = score_one + 2'b10;
						scorePoint = 0;
				end
			
			//判斷碰撞
			//情況一 頭對頭
			//bad jet1
			if((x_jet == x_badjet && y_badjet == 2)&& !BadBreak)
				isCrush = 1;
			//bad jet2
			if((x_jet == x_badjet2 && y_badjet == 2)&&!BadBreak2)
				isCrush2 = 1;
			//情況二 身對頭
			else if ((x_jet == x_badjet - 1 || x_jet == x_badjet + 1)&& y_badjet == 1 && !BadBreak )
				isCrush = 1;
			else if ((x_jet == x_badjet2 - 1 || x_jet == x_badjet2 + 1)&& y_badjet == 1 && !BadBreak2)
				isCrush2 = 1;
			//情況三 身對身
			else if ((x_jet == x_badjet - 2 || x_jet == x_badjet + 2) && y_badjet == 0 && !BadBreak)
				isCrush = 1;
			else if ((x_jet == x_badjet2 - 2 || x_jet == x_badjet2 + 2) && y_badjet == 0 && !BadBreak2)
				isCrush2 = 1;
			else if(y_badjet == 8)
				begin
					isCrush = 0;
					isCrush2 = 0;
				end	
		end
		
		
	always @(posedge CLK_div1k) //畫面
		begin
			//設定reset
			if(ResetButton == 1)
				begin
					x_count = 3'b000;
					DATA_R = 8'b11111111;
					DATA_G = 8'b11111111;
					DATA_B = 8'b11111111;
					COMM = 3'b000;
					enable = 0;
					score_enable = 0;
					alphaCount = 0;
					alphaCD = 0;
					alpha = CLEAR;
				end
			else
				enable = 1;
			
			
			//畫面刷新
			if (x_count >= 7)
				x_count = 3'b000;
			else
				x_count = x_count + 1'b1;
			COMM = x_count;
			
			//通關畫面
			if(score > 19)
				begin
					DATA_B = 8'b11111111;
					DATA_R = 8'b11111111; 
					DATA_G = 8'b11111111;
					if(alphaCD < 500)
						alphaCD = alphaCD + 1;
					else
						begin
							alphaCD = 0;
							if(alphaCount == 0)
								alpha = V;
							else if(alphaCount == 1)
								alpha = I;
							else if(alphaCount == 2)
								alpha = C;
							else if(alphaCount == 3)
								alpha = T;
							else if(alphaCount == 4)
								alpha = O;
							else if(alphaCount == 5)
								alpha = R;
							else if(alphaCount == 6)
								alpha = Y;
							else 
								alphaCount = -1;
							alphaCount = alphaCount + 1;
						end
					DATA_G <= alpha[x_count];
					screenCOM = 2'b11;
				end
			//失敗畫面
			else if(life == 4'b0000)
				begin
					DATA_B = 8'b11111111;
					DATA_R = 8'b11111111; 
					DATA_G = 8'b11111111;
					if(alphaCD < 500)
						alphaCD = alphaCD + 1;
					else
					begin
						alphaCD = 0;
						if(alphaCount == 0)
							alpha = D;
						else if(alphaCount == 1)
							alpha = E;
						else if(alphaCount == 2)
							alpha = F;
						else if(alphaCount == 3)
							alpha = E;
						else if(alphaCount == 4)
							alpha = A;
						else if(alphaCount == 5)
							alpha = T;
						else
							alphaCount  = -1;
						alphaCount = alphaCount + 1;
					end
					DATA_R <= alpha[x_count];
					screenCOM = 2'b11;
				end
			//遊戲畫面
			else
				begin
					DATA_B = 8'b11111111;
					DATA_R = 8'b11111111; 
					DATA_G = 8'b11111111;
						//畫飛機(自己的)
						if (x_count == x_jet || x_count == x_jet - 1 || x_count == x_jet + 1)
							begin
								if(x_count == x_jet - 1 || x_count == x_jet + 1)
									begin
										DATA_R[1:0] = 2'b10;
										DATA_B[1:0] = 2'b10;
									end
								else if(x_count == x_jet)
									begin			
										DATA_R[1:0] = 2'b00;
										DATA_B[1:0] = 2'b00;
									end
							end
						else
							begin
								DATA_R[1:0] = 2'b11;
								DATA_B[1:0] = 2'b11;
							end
						
						//draw LaserBeam
						if (LaserBeam && x_count == x_jet)
							begin
								DATA_R[7:2] = 6'b000000;
								DATA_G[7:2] = 6'b000000;
							end
						//draw BigLaserBeam
						else if ((BigLaserBeam && x_count == x_jet) || (BigLaserBeam && x_count == x_jet + 1) || (BigLaserBeam && x_count == x_jet - 1))
							begin
								DATA_R[7:2] = 6'b000000;
								DATA_G[7:2] = 6'b000000;
							end
						else 
							begin
								DATA_R[7:2] = 6'b111111;
								DATA_G[7:2] = 6'b111111;
								DATA_B[7:2] = 6'b111111;
							end		
						//ball
						if(!isgone)
							begin
								if(x_count == x_ball)
									begin
										DATA_B[y_ball] = 1'b0;
									end
								else
									begin
										DATA_B[y_ball] = 1'b1;
									end
							end
						//draw Bad Jet1
						if(BadNum == 1 || BadNum == 2)
							begin
								if(!((BadBreak) || (isCrush)))
									begin
										if (x_count == x_badjet || x_count == x_badjet - 1 || x_count == x_badjet + 1)
											begin
												if(x_count == x_badjet)
													begin
														DATA_R[y_badjet] = 1'b0;
														DATA_R[y_badjet - 1] = 1'b0;
													end
												else if(x_count == x_badjet - 1 || x_count == x_badjet + 1)	
													begin
														DATA_R[y_badjet] = 1'b0;
														DATA_R[y_badjet - 1] = 1'b1;
													end
												else
													begin
														DATA_R[y_badjet] = 1'b1;
														DATA_R[y_badjet - 1] = 1'b1;
													end
											end
									end
							end
						//draw bad jet2
						if(BadNum == 2)
							begin
								if(!((BadBreak2) || (isCrush2)))
									begin
										if (x_count == x_badjet2 || x_count == x_badjet2 - 1 || x_count == x_badjet2 + 1)
											begin
												if(x_count == x_badjet2)
													begin
														DATA_R[y_badjet] = 1'b0;
														DATA_R[y_badjet - 1] = 1'b0;
													end
												else if(x_count == x_badjet2 - 1 || x_count == x_badjet2 + 1)	
													begin
														DATA_R[y_badjet] = 1'b0;
														DATA_R[y_badjet - 1] = 1'b1;
													end
												else
													begin
														DATA_R[y_badjet] = 1'b1;
														DATA_R[y_badjet - 1] = 1'b1;
													end
											end
									end
							end
						//show score
						if(score_enable == 0)
							begin
								 score_enable = 1;
								 screenCOM = 2'b01;
							end
						else 
							begin
								 score_enable = 0;
								 screenCOM = 2'b10;
							end
						if(score_enable == 0)
							begin
								case(score_one)
									 4'b0000:sevenScreen[6:0]=7'b0000001;
									 4'b0001:sevenScreen[6:0]=7'b1001111;
									 4'b0010:sevenScreen[6:0]=7'b0010010;
									 4'b0011:sevenScreen[6:0]=7'b0000110;
									 4'b0100:sevenScreen[6:0]=7'b1001100;
									 4'b0101:sevenScreen[6:0]=7'b0100100;
									 4'b0110:sevenScreen[6:0]=7'b0100000;
									 4'b0111:sevenScreen[6:0]=7'b0001111;
									 4'b1000:sevenScreen[6:0]=7'b0000000;
									 4'b1001:sevenScreen[6:0]=7'b0000100;
								endcase
							end
						else
							begin
								case(score_ten)
								 4'b0000:sevenScreen[6:0]=7'b0000001;
								 4'b0001:sevenScreen[6:0]=7'b1001111;
								 4'b0010:sevenScreen[6:0]=7'b0010010;
								 4'b0011:sevenScreen[6:0]=7'b0000110;
								 4'b0100:sevenScreen[6:0]=7'b1001100;
								endcase
							end
				end
		end

		
always @ (posedge CLK) // 6MHz分頻
	begin
		 if(counter6MHz == 4)
			begin
				counter6MHz = 0;
				clk_6MHz = ~clk_6MHz;
			end
		 else
			begin
				counter6MHz = counter6MHz + 1;
			end
	end

always @ (posedge CLK) // 8Hz分頻
	begin
		 if(counter8Hz == 3125000)
			begin
				counter8Hz = 0;
				clk_8Hz = ~clk_8Hz;
			end
		 else
			begin
				counter8Hz = counter8Hz + 1;
			end
	end

always @ (posedge clk_6MHz)
	begin
		if(count == 16383)
			begin
				  count = origin;
				  audiof = ~audiof;
			end
		else if (j == 24)
			begin
				audiof = 0;
				count = 0;
			end
		else
			count = count + 1;
	end


always @ (posedge clk_8Hz)
	begin
		case(j)
			'd1:origin = 'd4916;  		//1.//low
			'd2:origin = 'd6168;			//2.
			'd3:origin = 'd7281;  		//3.
			'd4:origin = 'd7791;			//4.
			'd5:origin = 'd8730; 		//5.
			'd6:origin = 'd9565;			//6.
			'd7:origin = 'd10310;		//7.
			  
			'd8:origin = 'd010647; 		//1  //middle
			'd9:origin = 'd011272; 		//2
			'd10:origin = 'd011831;		//3
			'd11:origin = 'd012087;		//4
			'd12:origin = 'd012556; 	//5
			'd13:origin = 'd012974; 	//6
			'd14:origin = 'd013346; 	//7
			  
			'd15:origin = 'd13516;  	//.1//high
			'd16:origin = 'd13829;		//.2
			'd17:origin = 'd14108;		//.3
			'd18:origin = 'd11535;		//.4
			'd19:origin = 'd14470;		//.5
			'd20:origin = 'd14678;		//.6
			'd21:origin = 'd14864;		//.7
			'd24:;
			default:origin = 'd011111;
		endcase             
	end
 
always @(posedge clk_8Hz)  //樂譜
	begin
		if(music_change == 0)
			begin
				len2 = 0;
				if(len1 == 156)
					len1 = 0;
				else
					len1 = len1 + 1;
				case(len1)
					0:j=13;
					1:j=13;
					2:j=13;
					3:j=13;
					4:j=15;
					5:j=15;
					6:j=14;
					7:j=14;
					8:j=14;
					9:j=14;
					10:j=12;
					11:j=12;
					12:j=24;
					13:j=12;
					14:j=12;
					15:j=13;
					16:j=13;
					17:j=13;
					18:j=13;
					19:j=24;
					20:j=13;
					21:j=13;
					22:j=15;
					23:j=15;
					24:j=14;
					25:j=14;
					26:j=14;
					27:j=14;
					28:j=12;
					29:j=12;
					30:j=24;
					31:j=24;
					32:j=17;
					33:j=17;
					34:j=17;
					35:j=17;
					36:j=15;
					37:j=15;
					38:j=16;
					39:j=16;
					40:j=16;
					41:j=16;   
					42:j=14;
					43:j=14;
					44:j=14;
					45:j=14;
					46:j=15;
					47:j=15;
					48:j=15;
					49:j=15;
					50:j=13;
					51:j=13;
					52:j=13;
					53:j=13;
					54:j=14;
					55:j=14;
					56:j=14;
					57:j=14;
					58:j=12;
					59:j=12;
					60:j=12;
					61:j=17;
					62:j=17;
					63:j=17;
					64:j=17;
					65:j=15;
					66:j=15;
					67:j=16;
					68:j=16;
					69:j=16;
					70:j=16;
					71:j=14;
					72:j=14;
					73:j=14;
					74:j=14;
					75:j=15;
					76:j=15;
					77:j=15;
					78:j=15;
					79:j=14;
					80:j=14;
					81:j=14;
					82:j=14;
					83:j=13;
					84:j=13;
					85:j=13;
					86:j=13;
					87:j=12;
					88:j=12;
					89:j=12;
					90:j=24;
					91:j=24;
					92:j=19;
					93:j=19;
					94:j=19;
					95:j=19;
					96:j=22;
					97:j=22;
					98:j=18;
					99:j=18;
					100:j=18;
					101:j=18;
					102:j=16;
					103:j=16;
					104:j=16;
					105:j=16;
					106:j=24;
					107:j=16;
					108:j=16;
					109:j=16;
					110:j=16;
					111:j=15;
					112:j=15;
					113:j=15;
					114:j=15;
					115:j=16;
					116:j=16;
					117:j=16;
					118:j=16;
					119:j=23;
					120:j=23;
					121:j=23;
					122:j=24;
					123:j=24;
					124:j=19;
					125:j=19;
					126:j=19;
					127:j=19;
					128:j=22;
					129:j=22;
					130:j=18;
					131:j=18;
					132:j=18;
					133:j=18;
					134:j=16;
					135:j=16;
					136:j=16;
					137:j=16;
					138:j=24;
					139:j=16;
					140:j=16;
					141:j=16;
					142:j=16;
					143:j=24;
					144:j=16;
					145:j=16;
					146:j=16;
					147:j=16;
					148:j=15;
					149:j=15;
					150:j=15;
					151:j=15;
					152:j=14;
					153:j=14;
					154:j=14;
					155:j=24;
				endcase	
			end
		else
			begin
				len1 = 0;
				if(len2 == 118)
					len2 = 0;
				else
					len2 = len2 + 1;
				case(len2)
					0:j=16;
					1:j=16;
					2:j=16;
					3:j=16;
					4:j=17;
					5:j=17;
					6:j=16;
					7:j=16;
					8:j=15;
					9:j=15;
					10:j=15;
					11:j=15;
					12:j=13;
					13:j=13;
					14:j=12;
					15:j=12;
					16:j=12;
					17:j=12;
					18:j=13;
					19:j=13;
					20:j=15;
					21:j=15;
					22:j=16;
					23:j=16;
					24:j=16;
					25:j=16;
					26:j=15;
					27:j=15;
					28:j=24;
					29:j=16;  //第二小節
					30:j=16;
					31:j=16;
					32:j=16;
					33:j=17;
					34:j=17;
					35:j=16;
					36:j=16;
					37:j=15;
					38:j=15;
					39:j=15;
					40:j=15;
					41:j=13;
					42:j=13;   
					43:j=12;
					44:j=12;
					45:j=12;
					46:j=12;
					47:j=13;
					48:j=13;
					49:j=15;
					50:j=15;
					51:j=18;
					52:j=18;
					53:j=18;
					54:j=18;
					55:j=17;
					56:j=17;
					57:j=24;
					58:j=16;  //第三小節
					59:j=16;
					60:j=16;
					61:j=16;
					62:j=17;
					63:j=17;
					64:j=16;
					65:j=16;
					66:j=15;
					67:j=15;
					68:j=15;
					69:j=15;
					70:j=13;
					71:j=13;
					72:j=12;
					73:j=12;
					74:j=12;
					75:j=12;
					76:j=13;
					77:j=13;
					78:j=15;
					79:j=15;
					80:j=16;
					81:j=16;
					82:j=16;
					83:j=16;
					84:j=15;
					85:j=15;
					86:j=24;
					87:j=18;  //第四小節
					88:j=18;
					89:j=18;
					90:j=18;
					91:j=17;
					92:j=17;
					93:j=16;
					94:j=16;
					95:j=17;
					96:j=17;
					97:j=17;
					98:j=17;
					99:j=16;
					100:j=16;
					101:j=15;
					102:j=15;
					103:j=13;
					104:j=13;
					105:j=15;
					106:j=15;
					107:j=16;
					108:j=16;
					109:j=15;
					110:j=15;
					111:j=18;
					112:j=18;
					113:j=18;
					114:j=18;
					115:j=17;
					116:j=17;
					117:j=24;
				endcase
			end
		            
	end
endmodule

module divfreq1kHz(input CLK, output reg CLK_div1k); //除頻器 1kHz //視覺暫留
	reg [24:0] Count;
	always @(posedge CLK)
		begin
			if(Count > 25000)
				begin
					Count <= 25'b0;
					CLK_div1k <= ~CLK_div1k;
				end
			else
				Count <= Count + 1'b1;
		end
endmodule

module Easy(input CLK, output reg CLK_div20); //除頻器 10Hz //簡單模式
	reg [24:0] Count;
	always @(posedge CLK)
		begin
			if(Count > 5000000)
				begin
					Count <= 25'b0;
					CLK_div20 <= ~CLK_div20;
				end
			else
				Count <= Count + 1'b1;
		end
endmodule

module Normal(input CLK, output reg CLK_div20); //除頻器 10Hz //普通模式
	reg [24:0] Count;
	always @(posedge CLK)
		begin
			if(Count > 2500000)
				begin
					Count <= 25'b0;
					CLK_div20 <= ~CLK_div20;
				end
			else
				Count <= Count + 1'b1;
		end
endmodule

module Hard(input CLK, output reg CLK_div20); //除頻器 20Hz //困難模式
	reg [24:0] Count;
	always @(posedge CLK)
		begin
			if(Count > 1250000)
				begin
					Count <= 25'b0;
					CLK_div20 <= ~CLK_div20;
				end
			else
				Count <= Count + 1'b1;
		end
endmodule