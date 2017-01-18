```
module judgement
(
    i_clk_50M,
	 i_rst,
	 led_agd,
	 led_duan,
	 o_light,
	 o_bell,
	 o_dp,
	 blood_count
);
//--------------------
input i_clk_50M;
input[2:0] led_agd;
input i_rst;
input[1:0] led_duan;
output reg[5:0] o_light;
output reg o_bell;
output reg blood_count;
output reg o_dp;
//------------------
reg[2:0] u_led_agd;
reg[2:0] u_led_duan;
reg[3:0] led_wei;
reg game_over;
reg[3:0] blood;
//-------------------
always @ ( *)
 blood_count=blood[3];
always @ (posedge i_clk_50M or negedge i_rst)
      begin
		 if(!i_rst)
		   begin
			u_led_agd<=3'b111;
			o_light<=6'b111111;
			u_led_duan<=2'b11;
			o_bell<=1'b1;
			end
		 else 
		   begin
			begin
		   u_led_agd<=led_agd;
			u_led_duan<=led_duan;
			end
			case(game_over)
			1'b0: 
			      begin
			      o_light=6'b111111;
					o_bell=1'b1;
					end
			1'b1: 
			       begin
			         o_light=6'b000000;
						o_bell=1'b0;
					end
			endcase
			end
		end	
//============================================================
always @ ( *)
      begin
		 if((!(u_led_agd[2]||u_led_duan[1]))||(!(u_led_agd[0]||u_led_duan[0])))
		   game_over=1'b1;
		 //else if(!(u_led_agd[0]||u_led_agd[0]))
		   //game_over=1'b1;
		 else 
		   game_over=1'b0;
       end
always @ ( *)
      begin
		if((!((u_led_agd[1])||u_led_duan[1]))||(!(u_led_agd[1]||u_led_duan[0])))
	      flag_r=1'b1;	
      else 
		  flag_r=1'b0;
		  end
reg tag_r;
always @ (posedge flag_r)
       begin
		 if(tag_r)
		   flag<=1'b1;
		 else
		   flag<=1'b0;
		 end
reg flag;
reg flag_r;
//---------------------------------------------------
always @ (posedge flag or negedge i_rst)
       begin
		 if(!i_rst)
		 blood<=4'b0;
		 else if(flag)
       blood<=blood+1'b1;
		 else if(blood==4'b1000)
		 blood<=4'b0;
		 else
		 blood<=blood;
		 end
//----------------------------------------------------------
always @ (posedge counter or negedge i_rst)//tag_r一秒脉冲
       begin
		   if(!i_rst)
			  tag_r<=1'b0;
		   else if(counter<=26'd516666666)
		     tag_r<=~tag_r;
			else
			  tag_r<=tag_r;
		 end
always @ (posedge i_clk_50M or negedge i_rst)
      begin
      	if(!i_rst)
			  counter<=26'b0;
			else if(counter==26'h2faf080)			  
			  counter<=26'b0;			  
			else 
			  counter<=counter+1'b1;
	   end
always @ (posedge i_clk_50M or negedge i_rst)
      begin
		  if(!i_rst)
		    counter_q<=26'b0;
		  else if(counter_q==26'd25000000)
		    counter_q<=26'b0;
		  else
		    counter_q<=counter_q+1'b1;
		end
always @ (posedge i_clk_50M or negedge i_rst)//counter_t每四分之一秒加满
      begin
		  if(!i_rst)
		    counter_t<=26'b0;
		  else if(counter_q==26'd12500000)
		    counter_t<=26'b0;
		  else 
		    counter_t<=counter_t+1'b1;
	   end
reg[25:0]counter_q;
reg[25:0]counter;
reg[25:0]counter_t;
reg tag;
reg tag_1;
always @ (posedge i_clk_50M or negedge i_rst)
      begin
		 if(!i_rst)
		   tag<=1'b0;
		 else if(counter_q<=26'd12500000)
		   tag<=1'b1;
		 else 
		   tag=1'b0;
		 end
always @ (posedge i_clk_50M or negedge i_rst)
        begin
		  if(!i_rst)
		    tag_1<=26'h0;
		  else if(counter_t<=26'd6750000)
		    tag_1<=1'b1;
		  else 
		    tag_1<=1'b0;
		  end 
//==============================================================================
always @ ( *)
     begin
	  case(blood)
	  4'b0000:
				  begin
					case(led_wei)
				  3'b000	:	o_dp=1'b0;
				  3'b001	:	o_dp=1'b0;
				  3'b010	:	o_dp=1'b0;
				  3'b011	:	o_dp=1'b0;
				  3'b100	:	o_dp=1'b0;
				  3'b101	:	o_dp=1'b0;
				  3'b110	:	o_dp=1'b0;
				  3'b111	:	o_dp=1'b0;
				  default: o_dp=1'b1;
					endcase
				  end
	  4'b0001:
				  begin
					case(led_wei)
				  3'b000	:	o_dp=1'b1;
				  3'b001	:	o_dp=1'b0;
				  3'b010	:	o_dp=1'b0;
				  3'b011	:	o_dp=1'b0;
				  3'b100	:	o_dp=1'b0;
				  3'b101	:	o_dp=1'b0;
				  3'b110	:	o_dp=1'b0;
				  3'b111	:	o_dp=1'b0;
				  default: o_dp=1'b1;
					endcase
				  end
	  4'b0010:
				  begin
					case(led_wei)
				  3'b000	:	o_dp=1'b1;
				  3'b001	:	o_dp=1'b1;
				  3'b010	:	o_dp=1'b0;
				  3'b011	:	o_dp=1'b0;
				  3'b100	:	o_dp=1'b0;
				  3'b101	:	o_dp=1'b0;
				  3'b110	:	o_dp=1'b0;
				  3'b111	:	o_dp=1'b0;
				  default: o_dp=1'b1;
					endcase
				  end
	  4'b0011:
				  begin
					case(led_wei)
				  3'b000	:	o_dp=1'b1;
				  3'b001	:	o_dp=1'b1;
				  3'b010	:	o_dp=1'b1;
				  3'b011	:	o_dp=1'b0;
				  3'b100	:	o_dp=1'b0;
				  3'b101	:	o_dp=1'b0;
				  3'b110	:	o_dp=1'b0;
				  3'b111	:	o_dp=1'b0;
				  default: o_dp=1'b1;
					endcase
				  end
     4'b0100:
				  begin
					case(led_wei)
				  3'b000	:	o_dp=1'b1;
				  3'b001	:	o_dp=1'b1;
				  3'b010	:	o_dp=1'b1;
				  3'b011	:	o_dp=1'b1;
				  3'b100	:	o_dp=1'b0;
				  3'b101	:	o_dp=1'b0;
				  3'b110	:	o_dp=1'b0;
				  3'b111	:	o_dp=1'b0;
				  default: o_dp=1'b1;
					endcase
				  end
	  4'b0101:
				  begin
					case(led_wei)
				  3'b000	:	o_dp=1'b1;
				  3'b001	:	o_dp=1'b1;
				  3'b010	:	o_dp=1'b1;
				  3'b011	:	o_dp=1'b1;
				  3'b100	:	o_dp=1'b1;
				  3'b101	:	o_dp=1'b0;
				  3'b110	:	o_dp=1'b0;
				  3'b111	:	o_dp=1'b0;
				  default: o_dp=1'b1;
					endcase
					end
		4'b0110:
				  begin
					case(led_wei)
				  3'b000	:	o_dp=1'b1;
				  3'b001	:	o_dp=1'b1;
				  3'b010	:	o_dp=1'b1;
				  3'b011	:	o_dp=1'b1;
				  3'b100	:	o_dp=1'b1;
				  3'b101	:	o_dp=1'b1;
				  3'b110	:	o_dp=1'b0;
				  3'b111	:	o_dp=1'b0;
				  default: o_dp=1'b1;
					endcase
					end
		4'b0111:
				  begin
					case(led_wei)
				  3'b000	:	o_dp=1'b1;
				  3'b001	:	o_dp=1'b1;
				  3'b010	:	o_dp=1'b1;
				  3'b011	:	o_dp=1'b1;
				  3'b100	:	o_dp=1'b1;
				  3'b101	:	o_dp=1'b1;
				  3'b110	:	o_dp=1'b1;
				  3'b111	:	o_dp=1'b0;
				  default: o_dp=1'b1;
					endcase
					end
		4'b1000:
				  begin
					case(led_wei)
				  3'b000	:	o_dp=1'b1;
				  3'b001	:	o_dp=1'b1;
				  3'b010	:	o_dp=1'b1;
				  3'b011	:	o_dp=1'b1;
				  3'b100	:	o_dp=1'b1;
				  3'b101	:	o_dp=1'b1;
				  3'b110	:	o_dp=1'b1;
				  3'b111	:	o_dp=1'b1;
				  default: o_dp=1'b1;
					endcase
					end
		default :
				  begin
					case(led_wei)
				  3'b000	:	o_dp=1'b1;
				  3'b001	:	o_dp=1'b1;
				  3'b010	:	o_dp=1'b1;
				  3'b011	:	o_dp=1'b1;
				  3'b100	:	o_dp=1'b1;
				  3'b101	:	o_dp=1'b1;
				  3'b110	:	o_dp=1'b1;
				  3'b111	:	o_dp=1'b1;
				  default: o_dp=1'b1;
					endcase
					end
	endcase
	end
//	  else if(game_over)
//	  begin
//		case(led_wei)
//	  3'b000	:	o_dp=1'b1;
//     3'b001	:	o_dp=1'b1;
//     3'b010	:	o_dp=1'b1;
//     3'b011	:	o_dp=1'b1;
//     3'b100	:	o_dp=1'b1;
//     3'b101	:	o_dp=1'b1;
//     3'b110	:	o_dp=1'b1;
//     3'b111	:	o_dp=1'b1;
//	  default: o_dp=1'b0;
//	   endcase
//	  end
//	  else
	//always @ (posedge i_clk_50M)
	//led_agd_1<=i_led_agd;
//assign cnt_clk_w=cnt_clk;

//assign a={i_led_agd[2],2'b01,i_led_agd[0],2'b11,i_led_agd[1],1'b1};
//assign b={i_led_agd[2],2'b10,i_led_agd[0],2'b11,i_led_agd[1],1'b1};
//assign c=led_agd_1[0];
reg[24:0] cnt_clk;
always @ (posedge i_clk_50M)
      if(cnt_clk==24'h20124)
		  led_wei<=led_wei+1'b1;	
		 else
		  led_wei<=led_wei;
//-----------------------------------------------------------
always @ (posedge i_clk_50M or negedge i_rst)
      begin
      	if(!i_rst)
			 begin
      	 cnt_clk<=24'b0;
			 end
      	else if(cnt_clk==24'h20124)
			 begin
			 cnt_clk<=24'b0;
			 end
			else
			 begin
      	 cnt_clk<=cnt_clk+1'b1;
			 end
      end
//always @ (posedge i_clk_50M or negedge i_rst)
//       begin
//		 if(!i_rst)
//		 begin
//       game_over<=1'b0;
//		 blood<=3'b0;
//		 end
//		 else
//		 begin
//		 game_over<=game_over_w;
//		 blood<=blood_w;
//		 end
//		 end
//assign o_light_w=o_light;
endmodule

      
```
