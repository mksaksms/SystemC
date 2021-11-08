![image](https://user-images.githubusercontent.com/55811995/140807188-bb469530-53d8-4135-af62-e43e8142c994.png)


Processess : 

1.They are simple c++ functions code which are executed concurrently (Similart to VHDL Process)
2.Denoted as SC_METHOD. 
3.Need a SC_CTOR (Refered as constructor to execute internal functions. ) 
4. This is the structure (should be saved as nand.h): 

#include "systemc.h"
SC_MODULE(nand2)          // declare nand2 sc_module
{
  sc_in<bool> A, B;       // input signal ports
  sc_out<bool> F;         // output signal ports

  void do_nand2()         // a C++ function
  {
    F.write( !(A.read() && B.read()) );
  }

  SC_CTOR(nand2)          // constructor for nand2
  {
    SC_METHOD(do_nand2);  // register do_nand2 with kernel
    sensitive << A << B;  // sensitivity list
  }
};
  
  
  
  
 To test a xor gate we can use this code : 

#include "systemc.h"
#include "nand2.h"
SC_MODULE(exor2)
{
  sc_in<bool> A, B;
  sc_out<bool> F;

  nand2 n1, n2, n3, n4;

  sc_signal<bool> S1, S2, S3;

  SC_CTOR(exor2) : n1("N1"), n2("N2"), n3("N3"), n4("N4")
  {
    n1.A(A);
    n1.B(B);
    n1.F(S1);

    n2.A(A);
    n2.B(S1);
    n2.F(S2);

    n3.A(S1);
    n3.B(B);
    n3.F(S3);

    n4.A(S2);
    n4.B(S3);
    n4.F(F);
  }
};
  
  
  To test IT : 
  1. sc_stop will stop the simulation . 


#include "systemc.h"
SC_MODULE(stim)
{
  sc_out<bool> A, B;
  sc_in<bool> Clk;

  void StimGen()
  {
    A.write(false);
    B.write(false);
    wait();
    A.write(false);
    B.write(true);
    wait();
    A.write(true);
    B.write(false);
    wait();
    A.write(true);
    B.write(true);
    wait();
    sc_stop();
  }
  SC_CTOR(stim)
  {
    SC_THREAD(StimGen);
    sensitive << Clk.pos();
  }
};

  
  
  TOP LEVEL : 

#include "systemc.h"
#include "stim.h"
#include "exor2.h"
#include "mon.h"

int sc_main(int argc, char* argv[])
{
  sc_signal<bool> ASig, BSig, FSig;
  sc_clock TestClk("TestClock", 10, SC_NS,0.5);

  stim Stim1("Stimulus");
  Stim1.A(ASig);
  Stim1.B(BSig);
  Stim1.Clk(TestClk);

  exor2 DUT("exor2");
  DUT.A(ASig);
  DUT.B(BSig);
  DUT.F(FSig);

  mon Monitor1("Monitor");
  Monitor1.A(ASig);
  Monitor1.B(BSig);
  Monitor1.F(FSig);
  Monitor1.Clk(TestClk);

  sc_start();  // run forever

  return 0;

}
  
  
 
