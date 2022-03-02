# CompArch_Lab4_ALU

//code for ALU (Top-Level Entity)

module ALU (X,Y,Cin, Op, Sum, Status);
parameter N = 31;
input [N:0]X;
input [N:0]Y;
input Cin;
input [2:0] Op;

output [N:0] Sum;
output reg [3:0] Status;

wire carry[32:0];
wire [N:0] I0, I1, I2, I3,I4,I5,I6,I7;

assign m0 = X^Y;
assign m1 = X&Y;
assign m2 = X|Y;
assign m3 = (~X)|Y;

generate
	genvar i;
		for(i =0; i< 32 ; i = i+1)
		begin: full_adder
		ADDER inst0 (X[i], Y[i], carry[i], I4[i], carry[i+1]);//X,Y,Cin,S,Cout
	end
endgenerate

SHIFTER inst32(X, Y, I5, I6);

assign m7 = 32'b0;

MULTIPLEXER inst33 (m0, m1, m2, m3, m4, m5, m6, m7, Op, Sum);

always @ (X or Y) begin
	if( (X[N] == 1 && Y[N] == 1) || (X[N] == 0 && Y[N] == 0))
		Status[0] = 1'b1;
	else 
		Status[0] = 1'b0;
end


always @(carry[32]) 
	Status[3] = carry[32];

always @(Sum[31]) 
	Status[1] = Sum[31];

always @ (Sum) begin
	if ( Sum == 32'b0)
		Status[2] = 1'b1;
	else
		Status[2] = 1'b0;
end
endmodule

//code for Multiplexer

module MUX(Out, Sel, m1, m2, m3, m4, m5, m6, m7, m8);

input [7:0] m1, m2, m3, m4, m5, m6, m7, m8;

input [2:0] Sel;

output [7:0] Out;

reg [7:0] Out;

always @ (m1 or m2 or m3 or m4 or m5 or m6 or m7 or m8 or Sel)

begin

case (Sel)

	3'b000 : Out = m1;
	3'b001 : Out = m2;
	3'b010 : Out = m3;
	3'b011 : Out = m4;
	3'b100 : Out = m5;
	3'b101 : Out = m6;
	3'b110 : Out = m7;
	3'b111 : Out = m8;
	
	default : Out = 8'bx;
	
	endcase
	
end
endmodule

//code for Adder

module ADDER(X, Y, Cin, S, Cout);

input X, Y;
input Cin;

output reg S;
output reg Cout;

always @(X or Y or Cin)
begin
{ Cout, S } = (X + Y + Cin);
end
endmodule

//code for Shifter
module SHIFTER(A, B, rOut, lOut);
input [31:0]A;
input [4:0]B;

output [31:0]rOut;
output [31:0]lOut;

assign lOut = A<<B;
assign rOut = A>>B;

endmodule
