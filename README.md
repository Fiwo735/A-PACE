# A-PACE: Attention Precision & Accumulation Co-design Explorer

Our project contains Verilog source code for our parametrisable design in `src/`. However, the hardware synthesis results have already been obtained using Vivado (so you don't have to wait for dozens of hours to regenerate our results) and they have been committed, essentially becoming data artifact. Hence, you can very efficiently run our tool on an example design configuration.

`DSE.py` is the top level code, it should be self explanatory - our analytical hardware resource model is first calibrated on pre-collected data. Then, you specify a single design to predict to verify the operation - normally our framework performs an exploration across dozens of designs. After that, you predict hardware resources (LUTs/FFs) by calling the appropriate function. You can also run the perplexity measurement if you uncomment the relevant function call (which internally calls python on `quant/llama_ppl.py`), but that requires correct CUDA environment and is somewhat slow (albeit still faster than running hardware synthesis).

Below that code for predicting hardware resources for a single design configuration, you can also see logic responsible for automatically finding all hardware synthesis results found in the specified directory. You also process the results, which means you parse all the hardware synthesis reports and perplexity values. Then, you call the plotting function, which handles the plot generation, which includes computing the Pareto front and selecting best candidate design according to our heuristic.

## How to run
### Simulation
```
A-PACE:~$ ./tb/dot/<module_name>/run_sim.sh
```

### Synthesis
```
A-PACE:~$ nohup <vivado_path> -mode batch -source ./src/attention/run_synth_fp.tcl -tclargs 2048 2048 32 32 32 8 4 3 4 3 4 3 NEUMAIER TWOSUM KLEIN yes yes yes > nohup_large.out 2>&1 &
```

Example `<vivado_path>`: `/mnt/applications/Xilinx/24.2/Vivado/2024.2/bin/vivado`

### DSE
First time:
```
A-PACE:~$ python3 -m venv venv
A-PACE:~$ source venv/bin/activate
A-PACE:~$ pip install -r requirements.txt
A-PACE:~$ ./install_kernels.sh
A-PACE:~$ python DSE.py --verbose
```

Afterwards:
```
A-PACE:~$ source venv/bin/activate
A-PACE:~$ python DSE.py --verbose
```

For long running jobs:
```
A-PACE:~$ source venv/bin/activate
A-PACE:~$ nohup python -u DSE.py --verbose > DSE_run_$(date +%F_%H-%M-%S).log 2>&1 &
[1] XXXXXXX
```

Check XXXXXXX process status:
```
A-PACE:~$ ps -fp XXXXXXX
```