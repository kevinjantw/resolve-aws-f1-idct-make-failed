## Start your FPGA Developer AMI (2021.2) machine
[Start your FPGA Developer AMI (2021.2) EC2 instance from AWS marketplace.](https://aws.amazon.com/marketplace/pp/prodview-gimv3gqbpe57k?sr=0-1&ref_=beagle&applicationId=AWSMPContessa)

<img src="https://github.com/kevinjantw/resolve-aws-f1-idct-make-failed/assets/11850122/f620fbcb-2fb7-4cca-a534-3231bffc23d2" width=60%>

## Setup Vitis
```console
$ git clone https://github.com/aws/aws-fpga.git $AWS_FPGA_REPO_DIR
$ cd $AWS_FPGA_REPO_DIR
$ source vitis_setup.sh
```

## Git clone Vitis-AWS-F1-Developer-Labs
```console
$ export LAB_WORK_DIR=/home/centos/src/project_data/
$ cd $LAB_WORK_DIR
$ cd aws-fpga  
$ git clone https://github.com/Xilinx/Vitis-AWS-F1-Developer-Labs.git
```

## Make Failed of idct 
```console
[centos@ip-172-31-85-116 aws-fpga]$ cd Vitis-AWS-F1-Developer-Labs/modules/module_01/idct
[centos@ip-172-31-85-116 idct]$ make run TARGET=hw_emu
Makefile:90: [WARNING]: g++ version older. Using g++ provided by the tool : /opt/Xilinx/Vivado/2021.2/tps/lnx64/gcc-6.2.0/bin/g++
mkdir -p ./build_hw_emu
/opt/Xilinx/Vivado/2021.2/tps/lnx64/gcc-6.2.0/bin/g++ -I./includes/xcl2 -I/opt/xilinx/xrt/include/ -I/opt/Xilinx/Vivado/2021.2/include/ -I/opt/Xilinx/Vivado/2021.1/include/ -I./src -O0 -g -Wall -fmessage-length=0 -std=c++11 ./includes/xcl2/xcl2.cpp -o build_hw_emu/host.exe ./src/host.cpp  ./src/idct.cpp -L/opt/xilinx/xrt/lib/ -lOpenCL -lpthread -lrt -lstdc++ -lxilinxopencl
In file included from ./src/host.cpp:39:0:
./src/types.hpp:35:20: fatal error: ap_int.h: No such file or directory
 #include <ap_int.h>
                    ^
compilation terminated.
In file included from ./src/idct.hpp:45:0,
                 from ./src/idct.cpp:40:
./src/types.hpp:35:20: fatal error: ap_int.h: No such file or directory
 #include <ap_int.h>
                    ^
compilation terminated.
make: *** [build_hw_emu/host.exe] Error 1
```
## Workaround
```console
[centos@ip-172-31-85-116 ~]$ cd /opt/Xilinx/Vivado
[centos@ip-172-31-85-116 Vivado]$ sudo mkdir 2021.1
[centos@ip-172-31-85-116 Vivado]$ cd 2021.1
[centos@ip-172-31-85-116 2021.1]$ sudo tar -vxf 2021.1.include.tar
```
```console
[centos@ip-172-31-85-116 aws-fpga]$ pwd
/home/centos/src/project_data/aws-fpga
[centos@ip-172-31-85-116 aws-fpga]$ cd Vitis-AWS-F1-Developer-Labs/modules/module_01/idct
[centos@ip-172-31-85-116 idct]$ vi Makefile (add 'CXXFLAGS += -I/opt/Xilinx/Vivado/2021.1/include/' and saved Makefile)
```
<img src="https://github.com/kevinjantw/resolve-aws-f1-idct-make-failed/assets/11850122/eacea946-191e-4305-b81a-23fb506d9e2c" width=40%>

## Make Successful of idct
```console
[centos@ip-172-31-85-116 idct]$ make run TARGET=hw_emu
Makefile:90: [WARNING]: g++ version older. Using g++ provided by the tool : /opt/Xilinx/Vivado/2021.2/tps/lnx64/gcc-6.2.0/bin/g++
mkdir -p ./build_hw_emu
/opt/Xilinx/Vivado/2021.2/tps/lnx64/gcc-6.2.0/bin/g++ -I./includes/xcl2 -I/opt/xilinx/xrt/include/ -I/opt/Xilinx/Vivado/2021.2/include/ -I/opt/Xilinx/Vivado/2021.1/include/ -I./src -O0 -g -Wall -fmessage-length=0 -std=c++11 ./includes/xcl2/xcl2.cpp -o build_hw_emu/host.exe ./src/host.cpp  ./src/idct.cpp -L/opt/xilinx/xrt/lib/ -lOpenCL -lpthread -lrt -lstdc++ -lxilinxopencl
In file included from ./src/host.cpp:41:0:
./src/krnl_idct_wrapper.hpp: In function ‘void runFPGA(std::atomic<bool>&, int, int, int, size_t, size_t, cl::Context&, cl::CommandQueue*, cl::Kernel&, std::vector<short int, aligned_allocator<short int> >&, std::vector<short unsigned int, aligned_allocator<short unsigned int> >&, std::vector<short int, aligned_allocator<short int> >&)’:
./src/krnl_idct_wrapper.hpp:65:10: warning: unused variable ‘schedulingBufferFull’ [-Wunused-variable]
     bool schedulingBufferFull = false;
          ^~~~~~~~~~~~~~~~~~~~
./src/krnl_idct_wrapper.hpp:48:1: warning: label ‘EVENTS_AND_WAIT_LISTS’ defined but not used [-Wunused-label]
 EVENTS_AND_WAIT_LISTS:
 ^~~~~~~~~~~~~~~~~~~~~
./src/krnl_idct_wrapper.hpp:68:1: warning: label ‘BATCH_PROCESSING_LOOP’ defined but not used [-Wunused-label]
 BATCH_PROCESSING_LOOP:
 ^~~~~~~~~~~~~~~~~~~~~
./src/host.cpp: In function ‘int main(int, char**)’:
./src/host.cpp:192:1: warning: label ‘FPGA_DEVICE_OPENCL_SETUP’ defined but not used [-Wunused-label]
 FPGA_DEVICE_OPENCL_SETUP:
 ^~~~~~~~~~~~~~~~~~~~~~~~
./src/host.cpp:233:1: warning: label ‘CREATE_KERNEL’ defined but not used [-Wunused-label]
 CREATE_KERNEL:
 ^~~~~~~~~~~~~
cp ./xrt.ini ./build_hw_emu/xrt.ini
mkdir -p ./build_hw_emu
v++ -t hw_emu --platform /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm -R1 --save-temps --temp_dir ./build_hw_emu --log_dir ./build_hw_emu --profile_kernel data:all:all:all:all --trace_memory DDR[3] --config krnl_idct.cfg -c -k krnl_idct -I./src ./src/krnl_idct.cpp -o build_hw_emu/krnl_idct.hw_emu.xo
WARNING: [v++ 60-1603] The supplied option 'profile_kernel' is deprecated. The supplied option is replaced by an alternative feature controlled by option ''profile.*''. New options extend profiling beyond kernel and compute units to include additional interfaces.
WARNING: [v++ 60-1603] The supplied option 'trace_memory' is deprecated. The supplied option is replaced by an alternative feature controlled by option ''profile.trace_memory''. New options extend profiling beyond kernel and compute units to include additional interfaces.
Option Map File Used: '/opt/Xilinx/Vitis/2021.2/data/vitis/vpp/optMap.xml'
WARNING: [v++ 82-3893] Option --trace_memory ignored because --target hw not specified.

****** v++ v2021.2 (64-bit)
  **** SW Build 3363252 on 2021-10-14-04:41:01
    ** Copyright 1986-2020 Xilinx, Inc. All Rights Reserved.

INFO: [v++ 60-1306] Additional information associated with this v++ compile can be found at:
        Reports: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct.hw_emu
        Log files: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu
Running Dispatch Server on port: 45163
INFO: [v++ 60-1548] Creating build summary session with primary output /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu.xo.compile_summary, at Fri Dec 29 06:39:35 2023
INFO: [v++ 60-1316] Initiating connection to rulecheck server, at Fri Dec 29 06:39:37 2023
INFO: [v++ 60-1315] Creating rulecheck session with output '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct.hw_emu/v++_compile_krnl_idct.hw_emu_guidance.html', at Fri Dec 29 06:39:40 2023
INFO: [v++ 60-895]   Target platform: /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm
INFO: [v++ 60-1578]   This platform contains Xilinx Shell Archive '/home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/hw/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xsa'
INFO: [v++ 74-78] Compiler Version string: 2021.2
INFO: [v++ 60-1302] Platform 'xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm' has been explicitly enabled for this release with an extended warranty.
INFO: [v++ 60-585] Compiling for hardware emulation target
INFO: [v++ 60-423]   Target device: xilinx_aws-vu9p-f1_shell-v04261818_201920_3
INFO: [v++ 60-242] Creating kernel: 'krnl_idct'

===>The following messages were generated while  performing high-level synthesis for kernel: krnl_idct Log file: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu/krnl_idct/vitis_hls.log :
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_73_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_73_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_73_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_73_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_291_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = 2, Final II = 2, Depth = 24, loop 'VITIS_LOOP_291_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_92_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_92_1'
INFO: [v++ 200-789] **** Estimated Fmax: 294.81 MHz
INFO: [v++ 60-594] Finished kernel compilation
INFO: [v++ 60-244] Generating system estimate report...
INFO: [v++ 60-1092] Generated system estimate report: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct.hw_emu/system_estimate_krnl_idct.hw_emu.xtxt
INFO: [v++ 60-586] Created build_hw_emu/krnl_idct.hw_emu.xo
INFO: [v++ 60-2343] Use the vitis_analyzer tool to visualize and navigate the relevant reports. Run the following command.
    vitis_analyzer /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu.xo.compile_summary
INFO: [v++ 60-791] Total elapsed time: 0h 3m 39s
INFO: [v++ 60-1653] Closing dispatch client.
mkdir -p ./build_hw_emu
v++ -t hw_emu --platform /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm -R1 --save-temps --temp_dir ./build_hw_emu --log_dir ./build_hw_emu --profile_kernel data:all:all:all:all --trace_memory DDR[3] --config krnl_idct.cfg -c -k krnl_idct_med -I./src ./src/krnl_idct_med.cpp -o build_hw_emu/krnl_idct_med.hw_emu.xo
WARNING: [v++ 60-1603] The supplied option 'profile_kernel' is deprecated. The supplied option is replaced by an alternative feature controlled by option ''profile.*''. New options extend profiling beyond kernel and compute units to include additional interfaces.
WARNING: [v++ 60-1603] The supplied option 'trace_memory' is deprecated. The supplied option is replaced by an alternative feature controlled by option ''profile.trace_memory''. New options extend profiling beyond kernel and compute units to include additional interfaces.
Option Map File Used: '/opt/Xilinx/Vitis/2021.2/data/vitis/vpp/optMap.xml'
WARNING: [v++ 82-3893] Option --trace_memory ignored because --target hw not specified.

****** v++ v2021.2 (64-bit)
  **** SW Build 3363252 on 2021-10-14-04:41:01
    ** Copyright 1986-2020 Xilinx, Inc. All Rights Reserved.

INFO: [v++ 60-1306] Additional information associated with this v++ compile can be found at:
        Reports: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct_med.hw_emu
        Log files: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_med.hw_emu
Running Dispatch Server on port: 43431
INFO: [v++ 60-1548] Creating build summary session with primary output /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_med.hw_emu.xo.compile_summary, at Fri Dec 29 06:43:16 2023
INFO: [v++ 60-1316] Initiating connection to rulecheck server, at Fri Dec 29 06:43:16 2023
INFO: [v++ 60-1315] Creating rulecheck session with output '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct_med.hw_emu/v++_compile_krnl_idct_med.hw_emu_guidance.html', at Fri Dec 29 06:43:17 2023
INFO: [v++ 60-895]   Target platform: /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm
INFO: [v++ 60-1578]   This platform contains Xilinx Shell Archive '/home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/hw/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xsa'
INFO: [v++ 74-78] Compiler Version string: 2021.2
INFO: [v++ 60-1302] Platform 'xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm' has been explicitly enabled for this release with an extended warranty.
INFO: [v++ 60-585] Compiling for hardware emulation target
INFO: [v++ 60-423]   Target device: xilinx_aws-vu9p-f1_shell-v04261818_201920_3
INFO: [v++ 60-242] Creating kernel: 'krnl_idct_med'

===>The following messages were generated while  performing high-level synthesis for kernel: krnl_idct_med Log file: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_med.hw_emu/krnl_idct_med/vitis_hls.log :
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_73_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_73_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_73_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_73_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_291_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = 4, Final II = 4, Depth = 30, loop 'VITIS_LOOP_291_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_92_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_92_1'
INFO: [v++ 200-789] **** Estimated Fmax: 294.81 MHz
INFO: [v++ 60-594] Finished kernel compilation
INFO: [v++ 60-244] Generating system estimate report...
INFO: [v++ 60-1092] Generated system estimate report: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct_med.hw_emu/system_estimate_krnl_idct_med.hw_emu.xtxt
INFO: [v++ 60-586] Created build_hw_emu/krnl_idct_med.hw_emu.xo
INFO: [v++ 60-2343] Use the vitis_analyzer tool to visualize and navigate the relevant reports. Run the following command.
    vitis_analyzer /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_med.hw_emu.xo.compile_summary
INFO: [v++ 60-791] Total elapsed time: 0h 1m 45s
INFO: [v++ 60-1653] Closing dispatch client.
mkdir -p ./build_hw_emu
v++ -t hw_emu --platform /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm -R1 --save-temps --temp_dir ./build_hw_emu --log_dir ./build_hw_emu --profile_kernel data:all:all:all:all --trace_memory DDR[3] --config krnl_idct.cfg -c -k krnl_idct_slow -I./src ./src/krnl_idct_slow.cpp -o build_hw_emu/krnl_idct_slow.hw_emu.xo
WARNING: [v++ 60-1603] The supplied option 'profile_kernel' is deprecated. The supplied option is replaced by an alternative feature controlled by option ''profile.*''. New options extend profiling beyond kernel and compute units to include additional interfaces.
WARNING: [v++ 60-1603] The supplied option 'trace_memory' is deprecated. The supplied option is replaced by an alternative feature controlled by option ''profile.trace_memory''. New options extend profiling beyond kernel and compute units to include additional interfaces.
Option Map File Used: '/opt/Xilinx/Vitis/2021.2/data/vitis/vpp/optMap.xml'
WARNING: [v++ 82-3893] Option --trace_memory ignored because --target hw not specified.

****** v++ v2021.2 (64-bit)
  **** SW Build 3363252 on 2021-10-14-04:41:01
    ** Copyright 1986-2020 Xilinx, Inc. All Rights Reserved.

INFO: [v++ 60-1306] Additional information associated with this v++ compile can be found at:
        Reports: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct_slow.hw_emu
        Log files: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_slow.hw_emu
Running Dispatch Server on port: 43635
INFO: [v++ 60-1548] Creating build summary session with primary output /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_slow.hw_emu.xo.compile_summary, at Fri Dec 29 06:45:04 2023
INFO: [v++ 60-1316] Initiating connection to rulecheck server, at Fri Dec 29 06:45:04 2023
INFO: [v++ 60-1315] Creating rulecheck session with output '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct_slow.hw_emu/v++_compile_krnl_idct_slow.hw_emu_guidance.html', at Fri Dec 29 06:45:05 2023
INFO: [v++ 60-895]   Target platform: /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm
INFO: [v++ 60-1578]   This platform contains Xilinx Shell Archive '/home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/hw/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xsa'
INFO: [v++ 74-78] Compiler Version string: 2021.2
INFO: [v++ 60-1302] Platform 'xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm' has been explicitly enabled for this release with an extended warranty.
INFO: [v++ 60-585] Compiling for hardware emulation target
INFO: [v++ 60-423]   Target device: xilinx_aws-vu9p-f1_shell-v04261818_201920_3
INFO: [v++ 60-242] Creating kernel: 'krnl_idct_slow'

===>The following messages were generated while  performing high-level synthesis for kernel: krnl_idct_slow Log file: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_slow.hw_emu/krnl_idct_slow/vitis_hls.log :
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_73_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_73_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_73_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_73_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_291_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = 8, Final II = 8, Depth = 27, loop 'VITIS_LOOP_291_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_92_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_92_1'
INFO: [v++ 200-789] **** Estimated Fmax: 294.81 MHz
INFO: [v++ 60-594] Finished kernel compilation
INFO: [v++ 60-244] Generating system estimate report...
INFO: [v++ 60-1092] Generated system estimate report: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct_slow.hw_emu/system_estimate_krnl_idct_slow.hw_emu.xtxt
INFO: [v++ 60-586] Created build_hw_emu/krnl_idct_slow.hw_emu.xo
INFO: [v++ 60-2343] Use the vitis_analyzer tool to visualize and navigate the relevant reports. Run the following command.
    vitis_analyzer /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_slow.hw_emu.xo.compile_summary
INFO: [v++ 60-791] Total elapsed time: 0h 1m 45s
INFO: [v++ 60-1653] Closing dispatch client.
mkdir -p ./build_hw_emu
v++ -t hw_emu --platform /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm -R1 --save-temps --temp_dir ./build_hw_emu --log_dir ./build_hw_emu --profile_kernel data:all:all:all:all --trace_memory DDR[3] --config krnl_idct.cfg -c -k krnl_idct_noflow -I./src ./src/krnl_idct_noflow.cpp -o build_hw_emu/krnl_idct_noflow.hw_emu.xo
WARNING: [v++ 60-1603] The supplied option 'profile_kernel' is deprecated. The supplied option is replaced by an alternative feature controlled by option ''profile.*''. New options extend profiling beyond kernel and compute units to include additional interfaces.
WARNING: [v++ 60-1603] The supplied option 'trace_memory' is deprecated. The supplied option is replaced by an alternative feature controlled by option ''profile.trace_memory''. New options extend profiling beyond kernel and compute units to include additional interfaces.
Option Map File Used: '/opt/Xilinx/Vitis/2021.2/data/vitis/vpp/optMap.xml'
WARNING: [v++ 82-3893] Option --trace_memory ignored because --target hw not specified.

****** v++ v2021.2 (64-bit)
  **** SW Build 3363252 on 2021-10-14-04:41:01
    ** Copyright 1986-2020 Xilinx, Inc. All Rights Reserved.

INFO: [v++ 60-1306] Additional information associated with this v++ compile can be found at:
        Reports: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct_noflow.hw_emu
        Log files: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_noflow.hw_emu
Running Dispatch Server on port: 36142
INFO: [v++ 60-1548] Creating build summary session with primary output /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_noflow.hw_emu.xo.compile_summary, at Fri Dec 29 06:46:51 2023
INFO: [v++ 60-1316] Initiating connection to rulecheck server, at Fri Dec 29 06:46:51 2023
INFO: [v++ 60-1315] Creating rulecheck session with output '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct_noflow.hw_emu/v++_compile_krnl_idct_noflow.hw_emu_guidance.html', at Fri Dec 29 06:46:53 2023
INFO: [v++ 60-895]   Target platform: /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm
INFO: [v++ 60-1578]   This platform contains Xilinx Shell Archive '/home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/hw/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xsa'
INFO: [v++ 74-78] Compiler Version string: 2021.2
INFO: [v++ 60-1302] Platform 'xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm' has been explicitly enabled for this release with an extended warranty.
INFO: [v++ 60-585] Compiling for hardware emulation target
INFO: [v++ 60-423]   Target device: xilinx_aws-vu9p-f1_shell-v04261818_201920_3
INFO: [v++ 60-242] Creating kernel: 'krnl_idct_noflow'

===>The following messages were generated while  performing high-level synthesis for kernel: krnl_idct_noflow Log file: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_noflow.hw_emu/krnl_idct_noflow/vitis_hls.log :
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_73_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_73_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_73_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_73_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_291_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = 2, Final II = 2, Depth = 24, loop 'VITIS_LOOP_291_1'
INFO: [v++ 204-61] Pipelining loop 'VITIS_LOOP_92_1'.
INFO: [v++ 200-1470] Pipelining result : Target II = NA, Final II = 1, Depth = 3, loop 'VITIS_LOOP_92_1'
INFO: [v++ 200-790] **** Loop Constraint Status: All loop constraints were satisfied.
INFO: [v++ 200-789] **** Estimated Fmax: 294.81 MHz
INFO: [v++ 60-594] Finished kernel compilation
INFO: [v++ 60-244] Generating system estimate report...
INFO: [v++ 60-1092] Generated system estimate report: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/krnl_idct_noflow.hw_emu/system_estimate_krnl_idct_noflow.hw_emu.xtxt
INFO: [v++ 60-586] Created build_hw_emu/krnl_idct_noflow.hw_emu.xo
INFO: [v++ 60-2343] Use the vitis_analyzer tool to visualize and navigate the relevant reports. Run the following command.
    vitis_analyzer /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_noflow.hw_emu.xo.compile_summary
INFO: [v++ 60-791] Total elapsed time: 0h 1m 32s
INFO: [v++ 60-1653] Closing dispatch client.
mkdir -p ./build_hw_emu
mkdir -p ./build_hw_emu/hw_emu
v++ -t hw_emu --platform /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm -R1 --save-temps --temp_dir ./build_hw_emu --log_dir ./build_hw_emu --profile_kernel data:all:all:all:all --trace_memory DDR[3] --config krnl_idct.cfg -l -o build_hw_emu/krnl_idct.hw_emu.awsxclbin ./build_hw_emu/krnl_idct.hw_emu.xo ./build_hw_emu/krnl_idct_med.hw_emu.xo ./build_hw_emu/krnl_idct_slow.hw_emu.xo ./build_hw_emu/krnl_idct_noflow.hw_emu.xo
WARNING: [v++ 60-1603] The supplied option 'profile_kernel' is deprecated. The supplied option is replaced by an alternative feature controlled by option ''profile.*''. New options extend profiling beyond kernel and compute units to include additional interfaces.
WARNING: [v++ 60-1603] The supplied option 'trace_memory' is deprecated. The supplied option is replaced by an alternative feature controlled by option ''profile.trace_memory''. New options extend profiling beyond kernel and compute units to include additional interfaces.
Option Map File Used: '/opt/Xilinx/Vitis/2021.2/data/vitis/vpp/optMap.xml'
WARNING: [v++ 82-3893] Option --trace_memory ignored because --target hw not specified.

****** v++ v2021.2 (64-bit)
  **** SW Build 3363252 on 2021-10-14-04:41:01
    ** Copyright 1986-2020 Xilinx, Inc. All Rights Reserved.

INFO: [v++ 60-897] Reading --xp value from platform: param:compiler.lockFlowCritSlackThreshold=0
INFO: [v++ 60-897] Reading --xp value from platform: vivado_param:hd.routingContainmentAreaExpansion=true
INFO: [v++ 60-897] Reading --xp value from platform: vivado_param:hd.supportClockNetCrossDiffReconfigurablePartitions=1
INFO: [v++ 60-897] Reading --xp value from platform: vivado_param:bitstream.enablePR=4123
INFO: [v++ 60-897] Reading --xp value from platform: vivado_param:physynth.ultraRAMOptOutput=false
INFO: [v++ 60-897] Reading --xp value from platform: vivado_prop:run.__KERNEL__.{STEPS.SYNTH_DESIGN.ARGS.MAX_URAM_CASCADE_HEIGHT}={1}
INFO: [v++ 60-897] Reading --xp value from platform: vivado_param:synth.elaboration.rodinMoreOptions={rt::set_parameter disableOregPackingUram true}
WARNING: [v++ 60-642] Invalid or deprecated parameter name: compiler.lockFlowCritSlackThreshold
INFO: [v++ 60-1306] Additional information associated with this v++ link can be found at:
        Reports: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/link
        Log files: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link
Running Dispatch Server on port: 40616
INFO: [v++ 60-1548] Creating build summary session with primary output /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu.awsxclbin.link_summary, at Fri Dec 29 06:48:27 2023
INFO: [v++ 60-1316] Initiating connection to rulecheck server, at Fri Dec 29 06:48:27 2023
INFO: [v++ 60-1315] Creating rulecheck session with output '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/link/v++_link_krnl_idct.hw_emu_guidance.html', at Fri Dec 29 06:48:29 2023
INFO: [v++ 60-895]   Target platform: /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm
INFO: [v++ 60-1578]   This platform contains Xilinx Shell Archive '/home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/hw/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xsa'
INFO: [v++ 74-78] Compiler Version string: 2021.2
INFO: [v++ 60-1302] Platform 'xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm' has been explicitly enabled for this release with an extended warranty.
INFO: [v++ 60-629] Linking for hardware emulation target
INFO: [v++ 60-423]   Target device: xilinx_aws-vu9p-f1_shell-v04261818_201920_3
INFO: [v++ 60-1332] Run 'run_link' status: Not started
INFO: [v++ 60-1443] [06:48:34] Run run_link: Step system_link: Started
INFO: [v++ 60-1453] Command Line: system_link --xo /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu.xo --xo /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_med.hw_emu.xo --xo /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_slow.hw_emu.xo --xo /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_noflow.hw_emu.xo -keep --config /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/syslinkConfig.ini --xpfm /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm --target emu --output_dir /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int --temp_dir /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link
INFO: [v++ 60-1454] Run Directory: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/run_link
INFO: [SYSTEM_LINK 60-1316] Initiating connection to rulecheck server, at Fri Dec 29 06:48:37 2023
INFO: [SYSTEM_LINK 82-70] Extracting xo v3 file /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu.xo
INFO: [SYSTEM_LINK 82-70] Extracting xo v3 file /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_med.hw_emu.xo
INFO: [SYSTEM_LINK 82-70] Extracting xo v3 file /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_slow.hw_emu.xo
INFO: [SYSTEM_LINK 82-70] Extracting xo v3 file /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct_noflow.hw_emu.xo
INFO: [SYSTEM_LINK 82-53] Creating IP database /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/_sysl/.cdb/xd_ip_db.xml
INFO: [SYSTEM_LINK 82-38] [06:48:38] build_xd_ip_db started: /opt/Xilinx/Vitis/2021.2/bin/build_xd_ip_db -ip_search 0  -sds-pf /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/emu/xilinx_aws-vu9p-f1_shell-v04261818_201920_1_emu.hpfm -clkid 0 -ip /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/iprepo/xilinx_com_hls_krnl_idct_slow_1_0,krnl_idct_slow -ip /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/iprepo/xilinx_com_hls_krnl_idct_1_0,krnl_idct -ip /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/iprepo/xilinx_com_hls_krnl_idct_noflow_1_0,krnl_idct_noflow -ip /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/iprepo/xilinx_com_hls_krnl_idct_med_1_0,krnl_idct_med -o /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/_sysl/.cdb/xd_ip_db.xml
INFO: [SYSTEM_LINK 82-37] [06:48:48] build_xd_ip_db finished successfully
Time (s): cpu = 00:00:10 ; elapsed = 00:00:10 . Memory (MB): peak = 2148.109 ; gain = 0.000 ; free physical = 10843 ; free virtual = 25431
INFO: [SYSTEM_LINK 82-51] Create system connectivity graph
INFO: [SYSTEM_LINK 82-102] Applying explicit connections to the system connectivity graph: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/cfgraph/cfgen_cfgraph.xml
INFO: [SYSTEM_LINK 82-38] [06:48:49] cfgen started: /opt/Xilinx/Vitis/2021.2/bin/cfgen  -sp krnl_idct_1.inBlocks:DDR[0] -sp krnl_idct_1.coeffs:DDR[0] -sp krnl_idct_1.outBlocks:DDR[1] -sp krnl_idct_med_1.inBlocks:DDR[0] -sp krnl_idct_med_1.coeffs:DDR[0] -sp krnl_idct_slow_1.outBlocks:DDR[1] -sp krnl_idct_slow_1.inBlocks:DDR[0] -sp krnl_idct_slow_1.coeffs:DDR[0] -sp krnl_idct_med_1.outBlocks:DDR[1] -sp krnl_idct_noflow_1.inBlocks:DDR[0] -sp krnl_idct_noflow_1.coeffs:DDR[0] -sp krnl_idct_noflow_1.outBlocks:DDR[1] -dmclkid 0 -r /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/_sysl/.cdb/xd_ip_db.xml -o /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/cfgraph/cfgen_cfgraph.xml
INFO: [CFGEN 83-0] Kernel Specs:
INFO: [CFGEN 83-0]   kernel: krnl_idct, num: 1  {krnl_idct_1}
INFO: [CFGEN 83-0]   kernel: krnl_idct_med, num: 1  {krnl_idct_med_1}
INFO: [CFGEN 83-0]   kernel: krnl_idct_noflow, num: 1  {krnl_idct_noflow_1}
INFO: [CFGEN 83-0]   kernel: krnl_idct_slow, num: 1  {krnl_idct_slow_1}
INFO: [CFGEN 83-0] Port Specs:
INFO: [CFGEN 83-0]   kernel: krnl_idct_1, k_port: inBlocks, sptag: DDR[0]
INFO: [CFGEN 83-0]   kernel: krnl_idct_1, k_port: coeffs, sptag: DDR[0]
INFO: [CFGEN 83-0]   kernel: krnl_idct_1, k_port: outBlocks, sptag: DDR[1]
INFO: [CFGEN 83-0]   kernel: krnl_idct_med_1, k_port: inBlocks, sptag: DDR[0]
INFO: [CFGEN 83-0]   kernel: krnl_idct_med_1, k_port: coeffs, sptag: DDR[0]
INFO: [CFGEN 83-0]   kernel: krnl_idct_slow_1, k_port: outBlocks, sptag: DDR[1]
INFO: [CFGEN 83-0]   kernel: krnl_idct_slow_1, k_port: inBlocks, sptag: DDR[0]
INFO: [CFGEN 83-0]   kernel: krnl_idct_slow_1, k_port: coeffs, sptag: DDR[0]
INFO: [CFGEN 83-0]   kernel: krnl_idct_med_1, k_port: outBlocks, sptag: DDR[1]
INFO: [CFGEN 83-0]   kernel: krnl_idct_noflow_1, k_port: inBlocks, sptag: DDR[0]
INFO: [CFGEN 83-0]   kernel: krnl_idct_noflow_1, k_port: coeffs, sptag: DDR[0]
INFO: [CFGEN 83-0]   kernel: krnl_idct_noflow_1, k_port: outBlocks, sptag: DDR[1]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_1.inBlocks to DDR[0] for directive krnl_idct_1.inBlocks:DDR[0]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_1.coeffs to DDR[0] for directive krnl_idct_1.coeffs:DDR[0]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_1.outBlocks to DDR[1] for directive krnl_idct_1.outBlocks:DDR[1]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_med_1.inBlocks to DDR[0] for directive krnl_idct_med_1.inBlocks:DDR[0]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_med_1.coeffs to DDR[0] for directive krnl_idct_med_1.coeffs:DDR[0]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_med_1.outBlocks to DDR[1] for directive krnl_idct_med_1.outBlocks:DDR[1]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_noflow_1.inBlocks to DDR[0] for directive krnl_idct_noflow_1.inBlocks:DDR[0]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_noflow_1.coeffs to DDR[0] for directive krnl_idct_noflow_1.coeffs:DDR[0]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_noflow_1.outBlocks to DDR[1] for directive krnl_idct_noflow_1.outBlocks:DDR[1]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_slow_1.inBlocks to DDR[0] for directive krnl_idct_slow_1.inBlocks:DDR[0]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_slow_1.coeffs to DDR[0] for directive krnl_idct_slow_1.coeffs:DDR[0]
INFO: [CFGEN 83-2228] Creating mapping for argument krnl_idct_slow_1.outBlocks to DDR[1] for directive krnl_idct_slow_1.outBlocks:DDR[1]
INFO: [SYSTEM_LINK 82-37] [06:48:55] cfgen finished successfully
Time (s): cpu = 00:00:06 ; elapsed = 00:00:06 . Memory (MB): peak = 2148.109 ; gain = 0.000 ; free physical = 10842 ; free virtual = 25432
INFO: [SYSTEM_LINK 82-52] Create top-level block diagram
INFO: [SYSTEM_LINK 82-38] [06:48:55] cf2bd started: /opt/Xilinx/Vitis/2021.2/bin/cf2bd  --linux --trace_buffer 1024 --input_file /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/cfgraph/cfgen_cfgraph.xml --ip_db /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/_sysl/.cdb/xd_ip_db.xml --cf_name dr --working_dir /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/_sysl/.xsd --temp_dir /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link --output_dir /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int --target_bd emu/pfm_dynamic/pfm_dynamic.bd
INFO: [CF2BD 82-31] Launching cf2xd: cf2xd -linux -trace-buffer 1024 -i /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/cfgraph/cfgen_cfgraph.xml -r /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/_sysl/.cdb/xd_ip_db.xml -o dr.xml
INFO: [CF2BD 82-28] cf2xd finished successfully
INFO: [CF2BD 82-31] Launching cf_xsd: cf_xsd -disable-address-gen -bd emu/pfm_dynamic/pfm_dynamic.bd -dn dr -dp /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/sys_link/_sysl/.xsd
INFO: [CF2BD 82-28] cf_xsd finished successfully
INFO: [SYSTEM_LINK 82-37] [06:49:00] cf2bd finished successfully
Time (s): cpu = 00:00:04 ; elapsed = 00:00:05 . Memory (MB): peak = 2148.109 ; gain = 0.000 ; free physical = 10836 ; free virtual = 25431
INFO: [v++ 60-1441] [06:49:00] Run run_link: Step system_link: Completed
Time (s): cpu = 00:00:25 ; elapsed = 00:00:26 . Memory (MB): peak = 2168.570 ; gain = 0.000 ; free physical = 10885 ; free virtual = 25481
INFO: [v++ 60-1443] [06:49:00] Run run_link: Step cf2sw: Started
INFO: [v++ 60-1453] Command Line: cf2sw -sdsl /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/sdsl.dat -rtd /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/cf2sw.rtd -nofilter /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/cf2sw_full.rtd -xclbin /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/xclbin_orig.xml -o /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/xclbin_orig.1.xml
INFO: [v++ 60-1454] Run Directory: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/run_link
INFO: [v++ 60-1441] [06:49:06] Run run_link: Step cf2sw: Completed
Time (s): cpu = 00:00:05 ; elapsed = 00:00:06 . Memory (MB): peak = 2168.570 ; gain = 0.000 ; free physical = 10884 ; free virtual = 25481
INFO: [v++ 60-1443] [06:49:06] Run run_link: Step rtd2_system_diagram: Started
INFO: [v++ 60-1453] Command Line: rtd2SystemDiagram
INFO: [v++ 60-1454] Run Directory: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/run_link
INFO: [v++ 60-1441] [06:49:06] Run run_link: Step rtd2_system_diagram: Completed
Time (s): cpu = 00:00:00 ; elapsed = 00:00:00.50 . Memory (MB): peak = 2168.570 ; gain = 0.000 ; free physical = 10879 ; free virtual = 25477
INFO: [v++ 60-1443] [06:49:06] Run run_link: Step vpl: Started
INFO: [v++ 60-1453] Command Line: vpl -t hw_emu -f /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm --remote_ip_cache /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/.ipcache -s --profile_kernel data:all:all:all:all --output_dir /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int --log_dir /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link --report_dir /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/link --config /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/vplConfig.ini -k /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/kernel_info.dat --webtalk_flag Vitis --temp_dir /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link --emulation_mode debug_waveform --no-info --iprepo /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/xo/ip_repo/xilinx_com_hls_krnl_idct_noflow_1_0 --iprepo /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/xo/ip_repo/xilinx_com_hls_krnl_idct_slow_1_0 --iprepo /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/xo/ip_repo/xilinx_com_hls_krnl_idct_1_0 --iprepo /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/xo/ip_repo/xilinx_com_hls_krnl_idct_med_1_0 --messageDb /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/run_link/vpl.pb /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/dr.bd.tcl
INFO: [v++ 60-1454] Run Directory: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/run_link

****** vpl v2021.2 (64-bit)
  **** SW Build 3363252 on 2021-10-14-04:41:01
    ** Copyright 1986-2020 Xilinx, Inc. All Rights Reserved.

INFO: [VPL 60-839] Read in kernel information from file '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/kernel_info.dat'.
WARNING: [VPL 60-642] Invalid or deprecated parameter name: compiler.lockFlowCritSlackThreshold
INFO: [VPL 74-78] Compiler Version string: 2021.2
INFO: [VPL 60-423]   Target device: xilinx_aws-vu9p-f1_shell-v04261818_201920_3
INFO: [VPL 60-1032] Extracting hardware platform to /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/vivado/vpl/.local/hw_platform
[06:49:29] Run vpl: Step create_project: Started
Creating Vivado project.
[06:49:38] Run vpl: Step create_project: Completed
[06:49:38] Run vpl: Step create_bd: Started
[06:50:32] Run vpl: Step create_bd: Completed
[06:50:32] Run vpl: Step update_bd: Started
[06:51:26] Run vpl: Step update_bd: Completed
[06:51:26] Run vpl: Step generate_target: Started
[06:52:41] Run vpl: Step generate_target: RUNNING...
[06:53:57] Run vpl: Step generate_target: RUNNING...
[06:54:13] Run vpl: Step generate_target: Completed
[06:54:13] Run vpl: Step config_hw_emu.gen_scripts: Started
[06:54:53] Run vpl: Step config_hw_emu.gen_scripts: Completed
[06:54:53] Run vpl: Step config_hw_emu.compile: Started
[06:56:08] Run vpl: Step config_hw_emu.compile: RUNNING...
[06:57:24] Run vpl: Step config_hw_emu.compile: RUNNING...
[06:58:39] Run vpl: Step config_hw_emu.compile: RUNNING...
[06:59:06] Run vpl: Step config_hw_emu.compile: Completed
[06:59:06] Run vpl: Step config_hw_emu.elaborate: Started
[07:00:22] Run vpl: Step config_hw_emu.elaborate: RUNNING...
[07:00:58] Run vpl: Step config_hw_emu.elaborate: Completed
[07:00:59] Run vpl: FINISHED. Run Status: config_hw_emu.elaborate Complete!
INFO: [v++ 60-1441] [07:00:59] Run run_link: Step vpl: Completed
Time (s): cpu = 00:00:14 ; elapsed = 00:11:53 . Memory (MB): peak = 2168.570 ; gain = 0.000 ; free physical = 9365 ; free virtual = 25390
INFO: [v++ 60-1443] [07:00:59] Run run_link: Step rtdgen: Started
INFO: [v++ 60-1453] Command Line: rtdgen
INFO: [v++ 60-1454] Run Directory: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/run_link
INFO: [v++ 60-991] clock name 'kernel2_clk/clk' (clock ID '1') is being mapped to clock name 'KERNEL_CLK' in the xclbin
INFO: [v++ 60-991] clock name 'kernel_clk/clk' (clock ID '0') is being mapped to clock name 'DATA_CLK' in the xclbin
INFO: [v++ 60-991] clock name 'clk_main_a0' (clock ID '') is being mapped to clock name 'clk_main_a0' in the xclbin
INFO: [v++ 60-1230] The compiler selected the following frequencies for the runtime controllable kernel clock(s) and scalable system clock(s): System (SYSTEM) clock: clk_main_a0 = 250, Kernel (KERNEL) clock: kernel2_clk/clk = 500, Kernel (DATA) clock: kernel_clk/clk = 300
INFO: [v++ 60-1453] Command Line: cf2sw -a /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/address_map.xml -sdsl /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/sdsl.dat -xclbin /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/xclbin_orig.xml -rtd /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/krnl_idct.hw_emu.rtd -o /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/krnl_idct.hw_emu.xml
INFO: [v++ 60-1652] Cf2sw returned exit code: 0
WARNING: [v++ 60-1455] Debuggable symbols are not generated successfully, clean up /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/consolidated.cf
INFO: [v++ 60-1441] [07:01:08] Run run_link: Step rtdgen: Completed
Time (s): cpu = 00:00:09 ; elapsed = 00:00:09 . Memory (MB): peak = 2168.570 ; gain = 0.000 ; free physical = 9323 ; free virtual = 25390
INFO: [v++ 60-1443] [07:01:08] Run run_link: Step xclbinutil: Started
INFO: [v++ 60-1453] Command Line: xclbinutil --add-section DEBUG_IP_LAYOUT:JSON:/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/debug_ip_layout.rtd --add-section BITSTREAM:RAW:/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/behav.xse --force --target hw_emu --key-value SYS:dfx_enable:false --add-section :JSON:/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/krnl_idct.hw_emu.rtd --append-section :JSON:/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/appendSection.rtd --add-section CLOCK_FREQ_TOPOLOGY:JSON:/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/krnl_idct.hw_emu_xml.rtd --add-section BUILD_METADATA:JSON:/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/krnl_idct.hw_emu_build.rtd --add-section EMBEDDED_METADATA:RAW:/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/krnl_idct.hw_emu.xml --add-section SYSTEM_METADATA:RAW:/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/systemDiagramModelSlrBaseAddress.json --key-value SYS:PlatformVBNV:xilinx_aws-vu9p-f1_shell-v04261818_201920_3 --output /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu.awsxclbin
INFO: [v++ 60-1454] Run Directory: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/run_link
XRT Build Version: 2.12.0 (2021.2)
       Build Date: 2022-01-11 19:12:26
          Hash ID: 723d9e7abbe3a2c374682dbb1a59c47f230f3ee2
Creating a default 'in-memory' xclbin image.

Section: 'DEBUG_IP_LAYOUT'(9) was successfully added.
Size   : 2744 bytes
Format : JSON
File   : '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/debug_ip_layout.rtd'

Section: 'BITSTREAM'(0) was successfully added.
Size   : 43704296 bytes
Format : RAW
File   : '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/behav.xse'

Section: 'MEM_TOPOLOGY'(6) was successfully added.
Format : JSON
File   : 'mem_topology'

Section: 'IP_LAYOUT'(8) was successfully added.
Format : JSON
File   : 'ip_layout'

Section: 'CONNECTIVITY'(7) was successfully added.
Format : JSON
File   : 'connectivity'

Section: 'CLOCK_FREQ_TOPOLOGY'(11) was successfully added.
Size   : 410 bytes
Format : JSON
File   : '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/krnl_idct.hw_emu_xml.rtd'

Section: 'BUILD_METADATA'(14) was successfully added.
Size   : 7916 bytes
Format : JSON
File   : '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/krnl_idct.hw_emu_build.rtd'

Section: 'EMBEDDED_METADATA'(2) was successfully added.
Size   : 29557 bytes
Format : RAW
File   : '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/krnl_idct.hw_emu.xml'

Section: 'SYSTEM_METADATA'(22) was successfully added.
Size   : 22061 bytes
Format : RAW
File   : '/home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/int/systemDiagramModelSlrBaseAddress.json'

Section: 'PARTITION_METADATA'(20) was successfully appended to.
Format : JSON
File   : 'partition_metadata'
Successfully wrote (43787976 bytes) to the output file: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu.awsxclbin
Leaving xclbinutil.
INFO: [v++ 60-1441] [07:01:09] Run run_link: Step xclbinutil: Completed
Time (s): cpu = 00:00:00.05 ; elapsed = 00:00:00.72 . Memory (MB): peak = 2168.570 ; gain = 0.000 ; free physical = 9277 ; free virtual = 25388
INFO: [v++ 60-1443] [07:01:09] Run run_link: Step xclbinutilinfo: Started
INFO: [v++ 60-1453] Command Line: xclbinutil --quiet --force --info /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu.xclbin.info --input /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu.awsxclbin
INFO: [v++ 60-1454] Run Directory: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/run_link
INFO: [v++ 60-1441] [07:01:09] Run run_link: Step xclbinutilinfo: Completed
Time (s): cpu = 00:00:00.48 ; elapsed = 00:00:00.54 . Memory (MB): peak = 2168.570 ; gain = 0.000 ; free physical = 9277 ; free virtual = 25388
INFO: [v++ 60-1443] [07:01:09] Run run_link: Step generate_sc_driver: Started
INFO: [v++ 60-1453] Command Line:
INFO: [v++ 60-1454] Run Directory: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/run_link
INFO: [v++ 60-1441] [07:01:09] Run run_link: Step generate_sc_driver: Completed
Time (s): cpu = 00:00:00 ; elapsed = 00:00:00 . Memory (MB): peak = 2168.570 ; gain = 0.000 ; free physical = 9277 ; free virtual = 25388
INFO: [v++ 60-244] Generating system estimate report...
INFO: [v++ 60-1092] Generated system estimate report: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/link/system_estimate_krnl_idct.hw_emu.xtxt
INFO: [v++ 60-586] Created build_hw_emu/krnl_idct.hw_emu.awsxclbin
INFO: [v++ 60-1307] Run completed. Additional information can be found in:
        Guidance: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/reports/link/v++_link_krnl_idct.hw_emu_guidance.html
        Steps Log File: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/link/link.steps.log

INFO: [v++ 60-2343] Use the vitis_analyzer tool to visualize and navigate the relevant reports. Run the following command.
    vitis_analyzer /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/krnl_idct.hw_emu.awsxclbin.link_summary
INFO: [v++ 60-791] Total elapsed time: 0h 12m 54s
INFO: [v++ 60-1653] Closing dispatch client.
emconfigutil --nd 1  --platform /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm --od ./build_hw_emu

****** configutil v2021.2 (64-bit)
  **** SW Build 3363252 on 2021-10-14-04:41:01
    ** Copyright 1986-2020 Xilinx, Inc. All Rights Reserved.

INFO: [ConfigUtil 60-895]   Target platform: /home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xpfm
INFO: [ConfigUtil 60-1578]   This platform contains Xilinx Shell Archive '/home/centos/src/project_data/aws-fpga/Vitis/aws_platform/xilinx_aws-vu9p-f1_shell-v04261818_201920_3/hw/xilinx_aws-vu9p-f1_shell-v04261818_201920_3.xsa'
INFO: [ConfigUtil 60-1032] Extracting hardware platform to ./build_hw_emu
emulation configuration file `emconfig.json` is created in ./build_hw_emu directory
cp xrt.ini ./build_hw_emu;
cd ./build_hw_emu && XCL_EMULATION_MODE=hw_emu ./host.exe  ./krnl_idct.hw_emu.awsxclbin 32 8 1 1

------ Identified run mode : hw_emu
=====================================================================
Total Input data to be processed = 32 KBs ( 0 MBs) (0 GBs)
IDCT block size = 64
Batch Size ( IDCT blocks processed per kernel call) = 32
Total Number of batches to be processed( Total Kernel Calls) = 8
Max. scheduled kernel calls(Overlap Compute and data transfers) = 1
Total Number blocks to be processed = 256
=====================================================================
------ Starting to allocate memory for CPU/FPGA data storage
------ Successfully allocated memory for input/ouput data storage.
------ Initializing input data arrays and DCT co-efficients...
------ Done Initilizing input data arrays.
=====================================================================
XRT build version: 2.12.0
Build hash: 723d9e7abbe3a2c374682dbb1a59c47f230f3ee2
Build date: 2022-01-11 19:12:26
Git branch: 2021.2
PID: 6753
UID: 1000
[Fri Dec 29 07:01:18 2023 GMT]
HOST: ip-172-31-85-116.ec2.internal
EXE: /home/centos/src/project_data/aws-fpga/Vitis-AWS-F1-Developer-Labs/modules/module_01/idct/build_hw_emu/host.exe
[XRT] WARNING: "profile" configuration in xrt.ini will be deprecated in the next release.  Please use "opencl_summary=true" to enable OpenCL profiling and "opencl_device_counter=true" for device counter data in OpenCL profile summary.
[XRT] WARNING: "timeline_trace" configuration in xrt.ini will be deprecated in the next release.  Please use "opencl_trace=true" to enable OpenCL trace.
Found Platform
Platform Name: Xilinx
INFO: Reading ./krnl_idct.hw_emu.awsxclbin
Loading: './krnl_idct.hw_emu.awsxclbin'
Trying to program device[0]: xilinx_aws-vu9p-f1_shell-v04261818_201920_3
INFO: [HW-EMU 01] Hardware emulation runs simulation underneath. Using a large data set will result in long simulation times. It is recommended that a small dataset is used for faster execution. The flow uses approximate models for Global memories and interconnect and hence the performance data generated is approximate.
configuring penguin scheduler mode
scheduler config ert(0), dataflow(1), slots(16), cudma(1), cuisr(0), cdma(0), cus(4)
[XRT] WARNING: Trace Buffer size is too big. The maximum size of 4095M will be used.
Device[0]: programming successful!
------Created Handle for Kernel: krnl_idct
=====================================================================
------ Launched CPU and FPGA Thread and Monitoring Execution.
=====================================================================

[FPGA Time(    17s ) : ]  [CPU Time(    1s ) : Done. ]

Execution Finished
=====================================================================
------ All Task finished !
------ Done with CPU and FPGA based IDCTs
------ Runs complete validating results
------ TEST PASSED ------
INFO::[ Vitis-EM 22 ] [Time elapsed: 3 minute(s) 5 seconds, Emulation time: 0.403334 ms]
Data transfer between kernel(s) and global memory(s)
krnl_idct_1:m_axi_gmem0-DDR[0]          RD = 32.000 KB              WR = 0.000 KB
krnl_idct_noflow_1:m_axi_gmem1-DDR[0]          RD = 0.000 KB               WR = 0.000 KB
krnl_idct_noflow_1:m_axi_gmem2-DDR[1]          RD = 0.000 KB               WR = 0.000 KB
krnl_idct_slow_1:m_axi_gmem0-DDR[0]          RD = 0.000 KB               WR = 0.000 KB
krnl_idct_slow_1:m_axi_gmem1-DDR[0]          RD = 0.000 KB               WR = 0.000 KB
krnl_idct_slow_1:m_axi_gmem2-DDR[1]          RD = 0.000 KB               WR = 0.000 KB
krnl_idct_1:m_axi_gmem1-DDR[0]          RD = 1.000 KB               WR = 0.000 KB
krnl_idct_1:m_axi_gmem2-DDR[1]          RD = 0.000 KB               WR = 32.000 KB
krnl_idct_med_1:m_axi_gmem0-DDR[0]          RD = 0.000 KB               WR = 0.000 KB
krnl_idct_med_1:m_axi_gmem1-DDR[0]          RD = 0.000 KB               WR = 0.000 KB
krnl_idct_med_1:m_axi_gmem2-DDR[1]          RD = 0.000 KB               WR = 0.000 KB
krnl_idct_noflow_1:m_axi_gmem0-DDR[0]          RD = 0.000 KB               WR = 0.000 KB

INFO: [HW-EMU 06-0] Waiting for the simulator process to exit
INFO: [HW-EMU 06-1] All the simulator processes exited successfully
```
## Produced Xclbin
```console
[centos@ip-172-31-85-116 idct]$ ls
build_hw_emu  includes  krnl_idct.cfg  Makefile  src  xcd.log  xclbin  xrc.log  xrt.ini
[centos@ip-172-31-85-116 idct]$ cd xclbin/
[centos@ip-172-31-85-116 xclbin]$ ls
krnl_idct.hw.awsxclbin
```
