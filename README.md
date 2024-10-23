# PISO

Design code:

module piso_shift_register (
    input wire clk,          
    input wire rst,          
    input wire load,         
    input wire [7:0] pdata,  
    output reg sout          
);

    reg [7:0] shift_reg;     
    always @(posedge clk or posedge rst) begin
        if (rst) begin
            shift_reg <= 8'b0;   
            sout <= 1'b0;        
        end
        else if (load) begin
            shift_reg <= pdata;  
        end
        else begin
            sout <= shift_reg[7];  
            shift_reg <= {shift_reg[6:0], 1'b0};  
        end
    end
endmodule

Test bench :

module piso_shift_register_tb;
    reg clk;
    reg rst;
    reg load;
    reg [7:0] pdata;
    wire sout;
    piso_shift_register uut (
        .clk(clk),
        .rst(rst),
        .load(load),
        .pdata(pdata),
        .sout(sout)
    );
    always begin
        #5 clk = ~clk;
    end
    initial begin
        clk = 0;
        rst = 0;
        load = 0;
        pdata = 8'b0;
        rst = 1;
        #10;
        rst = 0;
        #10;
        pdata = 8'b10101010;  
        load = 1;            
        #10;
        load = 0; 
        #80;
        pdata = 8'b11001100;
        load = 1;
        #10;
        load = 0;
        #80;
        rst = 1;
        #10;
        rst = 0;
        $stop;
    end
    initial begin
        $monitor("Time: %0d, rst = %b, load = %b, pdata = %b, sout = %b", 
                  $time, rst, load, pdata, sout);
    end
endmodule

