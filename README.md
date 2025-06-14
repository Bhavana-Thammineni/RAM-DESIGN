# RAM-DESIGN
COMPANY: CODTECH IT SOLUTIONS

NAME: THAMMINENI BHAVANA

INTERN ID: CT04DM1127

DOMAIN: VLSI

DURATION: 4 WEEEKS

MENTOR: NEELA SANTOSH

module single_port_sync_ram
  # (parameter ADDR_WIDTH = 4,
     parameter DATA_WIDTH = 32,
     parameter DEPTH = 16
    )

  ( 	input 					clk,
   		input [ADDR_WIDTH-1:0]	addr,
   		inout [DATA_WIDTH-1:0]	data,
   		input 					cs,
   		input 					we,
   		input 					oe
  );

  reg [DATA_WIDTH-1:0] 	tmp_data;
  reg [DATA_WIDTH-1:0] 	mem [DEPTH];

  always @ (posedge clk) begin
    if (cs & we)
      mem[addr] <= data;
  end

  always @ (posedge clk) begin
    if (cs & !we)
    	tmp_data <= mem[addr];
  end

  assign data = cs & oe & !wr ? tmp_data : 'hz;
endmodule

TEST BENCH
module tb;
  parameter ADDR_WIDTH = 4;
  parameter DATA_WIDTH = 16;
  parameter DEPTH = 16;

  reg clk;
  reg cs;
  reg we;
  reg oe;
  reg [ADDR_WIDTH-1:0] addr;
  wire [DATA_WIDTH-1:0] data;
  reg [DATA_WIDTH-1:0] tb_data;

  single_port_sync_ram #(.DATA_WIDTH(DATA_WIDTH)) u0
  ( 	.clk(clk),
                        	.addr(addr),
                        	.data(data),
                        	.cs(cs),
   							.we(we),
   							.oe(oe)
                         );


  always #10 clk = ~clk;
  assign data = !oe ? tb_data : 'hz;

  initial begin
    {clk, cs, we, addr, tb_data, oe} <= 0;

    repeat (2) @ (posedge clk);

    for (integer i = 0; i < 2**ADDR_WIDTH; i= i+1) begin
      repeat (1) @(posedge clk) addr <= i; we <= 1; cs <=1; oe <= 0; tb_data <= $random;
    end

    for (integer i = 0; i < 2**ADDR_WIDTH; i= i+1) begin
      repeat (1) @(posedge clk) addr <= i; we <= 0; cs <= 1; oe <= 1;
    end

    #20 $finish;
  end
endmodule
![Image](https://github.com/user-attachments/assets/563e23a9-1a50-4853-bfc6-6422407044d9)
