Normal out 

cout << "Hello vhai " << endl;   // (c++) 



Waveform Tracing

The SystemC library supports waveform tracing. This requires adding statements to the top level of your system (inside sc_main). Here is the top level from the Modules and Processes tutorial with waveform tracing (to a vcd, Value Change Dump, file).

  sc_trace_file* Tf;
  Tf = sc_create_vcd_trace_file("traces");
  ((vcd_trace_file*)Tf)->sc_set_vcd_time_unit(-9);
  sc_trace(Tf, ASig  , "A" );
  sc_trace(Tf, BSig  , "B" );
  sc_trace(Tf, FSig  , "F" );
  sc_trace(Tf, DUT.S1, "S1");
  sc_trace(Tf, DUT.S2, "S2");
  sc_trace(Tf, DUT.S3, "S3");
  
  
  
    sc_start();  // run forever
  sc_close_vcd_trace_file(Tf);
  return 0;
