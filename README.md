# fifo_bfm_topology-

WRITE FIFO BFM (TOPOLOGY_PRINT)
Write_fifo_tb_top();
import uvm_pkg::*;
`include "uvm_macros.svh"
`include "write_fifo_interface.sv"
`include "write_fifo_seq_item.sv"
`include "write_fifo_sequence.sv"
`include "write_fifo_sequencer.sv"
`include "write_fifo_driver.sv"
`include "write_fifo_monitor.sv"
`include "write_fifo_agent.sv"
`include "write_fifo_environment.sv"
`include "write_fifo_test.sv"
module top;
    bit clk,rst;
  initial begin
    clk=1;
   forever #5 clk=~clk;
  end
  initial  begin
      rst =0;
      repeat(2) @(posedge clk)
      rst =1;
 end
  fifo_if intf(clk,rst);
/*  fifo dut (.data_in(intf.data_in),.wr(intf.wr),.rd(intf.rd),.empty(intf.empty),.full(intf.full),.fifo_cnt(intf.fifo_cnt),.data_out(intf.data_out),.clk(intf.clk),.rst(intf.rst));*/
 initial begin 
   uvm_config_db#(virtual fifo_if)::set(null,"*","vif",intf);
    $dumpfile("dump.vcd"); 
    $dumpvars;
  end
  initial begin
    run_test("write_fifo_test");
  end
endmodule

WRITE_FIFO_INTERFACE
interface fifo_if(input clk,rst);
    logic [7:0]data_in;
  logic wr;
  logic rd;
  logic empty;
  logic full;
  logic [3:0]fifo_cnt;
  logic [7:0]data_out;

   /* clocking driver_cb @(posedge clk);
    default input#1 output #1;
    output data_in;
    output wr;
    output rd;
    input  empty;
    input  full;
    input  fifo_cnt;
    input  data_out;
  endclocking
  
  clocking monitor_cb @(posedge clk);
    default input#1 output #1;
    input  data_in;
    input  wr;
    input  rd;
   	input  empty;
    input  full;
    input  fifo_cnt;
    input  data_out;
  endclocking
    modport DRIVER(clocking driver_cb,input clk,rst);
   modport MONITOR(clocking monitor_cb,input clk,rst);
  */
  
    
endinterface 
Write_fifo_test
class write_fifo_test extends uvm_test;
    `uvm_component_utils(write_fifo_test);
  
  write_fifo_env env;
  write_fifo_sequence seqn;
 
   function new (string name="write_fifo_test", uvm_component parent);
     super.new (name, parent);
  endfunction
  
    function void build_phase(uvm_phase phase);
        super.build_phase(phase);
        env=write_fifo_env::type_id::create("env",this);       
  endfunction
  
  function void end_of_elaboration_phase(uvm_phase phase);
    super.end_of_elaboration_phase(phase);
    uvm_top.print_topology();
  endfunction
  
  task run_phase(uvm_phase phase);
    seqn = write_fifo_sequence::type_id::create("seqn",this);
    phase.raise_objection(this);
    seqn.start(env.ag1.seqr);
    #20;
    phase.drop_objection(this); 
  endtask
  
  
endclass





WRITE_FIFO_ENV
class write_fifo_env extends uvm_env;
  
  `uvm_component_utils(write_fifo_env);
  
 write_fifo_agent ag1;
    
   function new(string name, uvm_component parent);
         super.new(name, parent);
     endfunction
  
  function void build_phase(uvm_phase phase);
    super.build_phase(phase);
    ag1=write_fifo_agent::type_id::create("ag1",this);
  endfunction
  
   function void connect_phase(uvm_phase phase);
     super.connect_phase(phase);
   endfunction  
 
  endclass












WRITE_FIFO_AGENT
class write_fifo_agent extends uvm_agent;
  
  `uvm_component_utils(write_fifo_agent);
  
  write_fifo_sequencer seqr;
  write_fifo_driver drv;
  write_fifo_monitor mon_1;
  
  
  function new(string name ="write_fifo_agent", uvm_component parent);
         super.new(name, parent);
     endfunction
  
  function void build_phase(uvm_phase phase);
    super.build_phase (phase);
    seqr =write_fifo_sequencer::type_id::create("seqr",this);
    drv  =write_fifo_driver::type_id::create("drv",this);
    mon_1=write_fifo_monitor::type_id::create("mon_1",this);
  endfunction
  
  function void connect_phase(uvm_phase phase);
     super.connect_phase (phase);   
     drv.seq_item_port.connect(seqr.seq_item_export);
  endfunction
  
endclass






WRITE_FIFO_SEQUENCER
class write_fifo_sequencer extends uvm_sequencer#(write_fifo_seq_item);
    `uvm_component_utils(write_fifo_sequencer);
    function new(string name , uvm_component parent);
      super.new(name,parent);
     endfunction
  
  function void build_phase(uvm_phase phase);
    super.build_phase(phase);
  endfunction
endclass





















WRITE_FIFO_SEQ_ITEM
class write_fifo_seq_item extends uvm_sequence_item;
  rand bit [7:0]data_in;
  rand bit wr;
  rand bit rd;
   bit empty;
   bit  full;
   bit [3:0]fifo_cnt;
   bit [7:0]data_out;
  `uvm_object_utils_begin(write_fifo_seq_item);
  `uvm_field_int(data_in,UVM_ALL_ON)
  `uvm_field_int(wr,UVM_ALL_ON)
  `uvm_field_int(rd,UVM_ALL_ON)
  `uvm_field_int(empty,UVM_ALL_ON)
  `uvm_field_int(full,UVM_ALL_ON)
  `uvm_field_int(fifo_cnt,UVM_ALL_ON)
  `uvm_field_int(data_out,UVM_ALL_ON)
  `uvm_object_utils_end
  
  function new (string name="write_fifo_seq_item");
    super.new (name);
  endfunction
  function void display(string name);
    $display("---------------------------------------------");
    $display("%s",name);
    $display("---------------------------------------------");
    $display("wr=%0d,rd=%0d,data_in=%0d",wr,rd,data_in);
    $display("---------------------------------------------");
    $display("wr=%0d,rd=%0d,data_out=%0d",wr,rd,data_out);
    $display("---------------------------------------------");
   /* $display("fifo_cnt=%0d",fifo_cnt);
    $display("---------------------------------------------");
    $display("empty=%0d,full=%0d",empty,full);
    $display("---------------------------------------------");*/
      
  endfunction
  
  endclass
WRITE_FIFO_DRIVER
//`define DRIV_IF intf.DRIVER.driver_cb
class write_fifo_driver extends uvm_driver#(write_fifo_seq_item);
  `uvm_component_utils(write_fifo_driver)
     virtual fifo_if intf;
    write_fifo_seq_item pkt;
        function new(string name="write_fifo_driver", uvm_component parent);
           super.new(name, parent);
        endfunction
   function void build_phase(uvm_phase phase);
        super.build_phase(phase);
    uvm_config_db#(virtual fifo_if)::get(this,"","vif",intf);
  endfunction
  task reset();
    wait(!intf.rst);
    intf.data_in<=0;
    intf.wr<=0;
    intf.rd<=0;
     wait(intf.rst);
  endtask
   task run_phase(uvm_phase phase);
        reset();
    forever begin
     pkt=write_fifo_seq_item::type_id::create("pkt");
      seq_item_port.get_next_item(pkt);
       drive(pkt);
      seq_item_port.item_done();
      pkt.display("DRIVER");
    end
  endtask 
   task drive(write_fifo_seq_item pkt);
    @(posedge intf.clk);
    intf.wr<=pkt.wr;
    intf.rd<=pkt.rd;
    intf.data_in<=pkt.data_in;
  endtask
 endclass
WRITE_FIFO_MONITOR
  class write_fifo_monitor extends uvm_monitor;
    `uvm_component_utils(write_fifo_monitor);
    write_fifo_seq_item pkt;
    virtual fifo_if intf;
  uvm_analysis_port #(write_fifo_seq_item)item_collected_port;

  function new(string name="write_fifo_monitor",uvm_component parent);
     super.new(name,parent);
        item_collected_port=new("item_collected_port",this);
        endfunction
  
 
  virtual function void build_phase(uvm_phase phase);
    super.build_phase(phase);
    uvm_config_db#(virtual fifo_if)::get(this,"","vif",intf);
       
  endfunction
  
   task run_phase(uvm_phase phase);
     pkt=write_fifo_seq_item::type_id::create("pkt");
    @(posedge intf.clk);
     forever begin
       @(posedge intf.clk);
       pkt.wr<=intf.wr;
       pkt.rd<=intf.rd;
       pkt.data_in<=intf.data_in;
       item_collected_port.write(pkt);
       pkt.display("MONITOR_1");

     end
     endtask
  endclass
    

WRITE_FIFO_PRINT TOPOLOGY
UVM_INFO verilog_src/questa_uvm_pkg-1.2/src/questa_uvm_pkg.sv(277) @ 0: reporter [Questa UVM] QUESTA_UVM-1.2.3
# UVM_INFO verilog_src/questa_uvm_pkg-1.2/src/questa_uvm_pkg.sv(278) @ 0: reporter [Questa UVM]  questa_uvm::init(+struct)
# ** Warning: (vsim-PLI-3110) $dumpfile : This task can be called only once. Please use $fdumpfile()
# for multiple files.
#    Time: 0 ns  Iteration: 0  Process: /top/#INITIAL#36 File: testbench.sv Line: 38
# UVM_INFO @ 0: reporter [RNTST] Running test write_fifo_test...
# UVM_INFO @ 0: reporter [UVMTOP] UVM testbench topology:
# ----------------------------------------------------------------
# Name                         Type                    Size  Value
# ----------------------------------------------------------------
# uvm_test_top                 write_fifo_test         -     @471 
#   env                        write_fifo_env          -     @478 
#     ag1                      write_fifo_agent        -     @485 
#       drv                    write_fifo_driver       -     @602 
#         rsp_port             uvm_analysis_port       -     @617 
#         seq_item_port        uvm_seq_item_pull_port  -     @609 
#       mon_1                  write_fifo_monitor      -     @625 
#         item_collected_port  uvm_analysis_port       -     @632 
#       seqr                   write_fifo_sequencer    -     @493 
#         rsp_export           uvm_analysis_export     -     @500 
#         seq_item_export      uvm_seq_item_pull_imp   -     @594 
#         arbitration_queue    array                   0     -    
#         lock_queue           array                   0     -    
#         num_last_reqs        integral                32    'd1  
#         num_last_rsps        integral                32    'd1  
# ----------------------------------------------------------------

# UVM_INFO verilog_src/uvm-1.1d/src/base/uvm_objection.svh(1267) @ 150: reporter [TEST_DONE] 'run' phase is ready to proceed to the 'extract' phase
# 
# --- UVM Report Summary ---
# 
# ** Report counts by severity
# UVM_INFO :    5
# UVM_WARNING :    0
# UVM_ERROR :    0
# UVM_FATAL :    0
# ** Report counts by id
# [Questa UVM]     2
# [RNTST]     1
# [TEST_DONE]     1
# [UVMTOP]     1
# ** Note: $finish    : /usr/share/questa/questasim/linux_x86_64/../verilog_src/uvm-1.1d/src/base/uvm_root.svh(430)
#    Time: 150 ns  Iteration: 54  Instance: /top
# End time: 07:16:32 on Jul 21,2022, Elapsed time: 0:00:00
  
