The last article talked about how to run PARSEC Benchmark alone in the Linux system. This article describes how to configure and run PARSEC Benchmark in the GEM5 simulator (taking the ARPHA architecture as an example). PARSEC Benchmark needs to run in full system mode in GEM5, and its configuration method is similar to that in Section 2.4 of the previous article. Related tutorials can refer to: http://www.m5sim.org/PARSEC_benchmarks.

  1- First create a new folder to store the disk image of PARSEC Benchmark.
  
     mkdir full_system_images
     cd full_system_images
  
  2- Download the initial system file, unzip it, and rename the folder (rename optional).
  
    wget http://www.m5sim.org/dist/current/m5_system_2.0b3.tar.bz2
    tar jxvf m5_system_2.0b3.tar.bz2
    mv m5_system_2.0b3 system

After decompression, the directory structure of the file is as follows:

    system/
         binaries/
                 console
                 ts_osfpal
                 vmlinux
         disks/
               linux-bigswap2.img
               linux-latest.img
  
  3- Download the PARSEC Benchmark related files and replace the corresponding files in the system folder.
  
Download the linux kernel file corresponding to PARSEC and replace it with 'system/binaries/vmlinux'
      
       cd ./system/binaries/
       wget http://www.cs.utexas.edu/~parsec_m5/vmlinux_2.6.27-gcc_4.3.4
       rm vmlinux
       mv vmlinux_2.6.27-gcc_4.3.4 vmlinux
     
Download the PAL code file corresponding to PARSEC and replace it with 'system/binaries/ts_osfpal'

       wget http://www.cs.utexas.edu/~parsec_m5/tsb_osfpal
       rm ts_osfpal
       mv tsb_osfpal ts_osfpal
     
Download PARSEC-2.1 Disk Image and unzip
   
       cd ../disks/
       wget http://www.cs.utexas.edu/~parsec_m5/linux-parsec-2-1-m5-with-test-inputs.img.bz2
       bzip2 -b linux-parsec-2-1-m5-with-test-inputs.img.bz2

 4- The gem5 is a SysPaths.py and Benckmarks.py parsec file.
 
 Open SysPaths.py to configure the full path of the parsec disk image:
 
       vim ./configs/common/SysPaths.py
     
before fixing:
 
       path = [ ’/dist/m5/system’, ’/n/poolfs/z/dist/m5/system’ ]
     
After modification:

       path = [ ’/dist/m5/system’, ’/home/full_system_images/system’ ]

Open Benchmarks.py and modify the image file name:

       vim ./configs/common/Benchmarks.py
        
Before fixing:

       elif buildEnv['TARGET_ISA'] == 'alpha':
           return env.get('LINUX_IMAGE', disk('linux-latest.img'))

After modification:

        elif buildEnv['TARGET_ISA'] == 'alpha':
            return env.get('LINUX_IMAGE', disk('linux-parsec-2-1-m5-with-test-inputs.img'))

 5- Generate benchmark script file for running benchmark

Download the PARSEC script generation package and unzip it:

      wget http://www.cs.utexas.edu/~parsec_m5/TR-09-32-parsec-2.1-alpha-files.tar.gz
      tar zxvf TR-09-32-parsec-2.1-alpha-files.tar.gz

Generate script commands:

      ./writescripts.pl <benchmark> <nthreads>       // nthreads =  we use this unmber in the next step
    
There are the following 13 benchmarks:
   
      blackscholes
      bodytrack
      canneal
      dedup
      facesim
      ferret
      fluidanimate
      freqmine
      streamcluster
      swaptions
      vips
      x264
      rtview

 6- Run gem5 according to the generated script file:

      ./build/ALPHA/gem5.opt ./configs/example/fs.py -n <nthreads> --script=./path/to/runScript.rcS --caches --l2cache -F 5000000000
    
 7- Open a new window, use telnet to interact with gem5 simulation system
 
     telnet localhost 3456  

    
 8- my config
 
        ./build/ALPHA/gem5.opt ./configs/example/fs.py -n 4 --script=./benchmarks/ferret_4c_simdev.rcS  --caches --l2cache -F 5000000000 \
        --mem-type=SimpleMemory --cpu-type=AtomicSimpleCPU \
        --kernel=full_system_images/system/binaries/vmlinux
       -------------------------------------------------------------------------------------------------------------------------------------
        
      ./build/ALPHA/gem5.opt configs/example/fs.py --cpu-type=TimingSimpleCPU --script=/home/amirreza/benchmarks/ferret_4c_simdev.rcS --num-cpus=2 --kernel=/home/amirreza/full_system_images/system/binaries/vmlinux --disk-image=/home/amirreza/full_system_images/system/disks/linux-parsec-2-1-m5-with-test-inputs.img
             



### All Translate From https://github.com/Pfzuo/pfzuo.github.io/blob/master/_posts/GEM5/2016-6-6-Configure%20and%20run%20parsec%202.1%20benchmark%20in%20GEM5.md ###

