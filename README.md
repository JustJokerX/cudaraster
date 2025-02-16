
Purpose of this repository
--------------------------

Research experiments using / against the CUDA rasterizer. Initial commit is the
1.1 release, downloaded from http://code.google.com/p/cudaraster/

No scenes are included.

Original README follows:

High-Performance GPU Software Rasterization 1.1
-----------------------------------------------
Implementation by Tero Karras and Samuli Laine
Copyright 2010-2012 NVIDIA Corporation

This package contains full source code for the fast GPU-based software
rasterizer described in the following paper: 

    "High-Performance Software Rasterization on GPUs",
    Samuli Laine and Tero Karras,
    Proc. High-Performance Graphics 2011
    http://www.tml.tkk.fi/~samuli/publications/laine2011hpg_paper.pdf

The source code is licensed under New BSD License (see LICENSE), and
hosted by Google Code:

http://code.google.com/p/cudaraster/


Abstract
--------

In this paper, we implement an efficient, completely software-based graphics
pipeline on a GPU. Unlike previous approaches, we obey ordering constraints
imposed by current graphics APIs, guarantee hole-free rasterization, and
support multisample antialiasing. Our goal is to examine the performance
implications of not exploiting the fixed-function graphics pipeline, and to
discern which additional hardware support would benefit software-based
graphics the most.

We present significant improvements over previous work in terms of
scalability, performance, and capabilities. Our pipeline is malleable and
easy to extend, and we demonstrate that in a wide variety of test cases its
performance is within a factor of 2-8x compared to the hardware graphics
pipeline on a top of the line GPU.


System requirements
-------------------

- Microsoft Windows XP, Vista, or 7.

- At least 1GB of system memory.

- NVIDIA CUDA-compatible GPU with compute capability 2.0 and at least 512
  megabytes of RAM. GeForce GTX 480 is recommended.
  
- NVIDIA CUDA 4.0 or later.

- Microsoft Visual Studio 2010. Required even if you do not plan to build
  the source code, as the runtime CUDA compilation mechanism depends on it.


Instructions
------------

1. Install Visual Studio 2010. The Express edition can be downloaded from:
   http://www.microsoft.com/visualstudio/en-us/products/2010-editions/visual-cpp-express

2. Install the latest NVIDIA GPU drivers and CUDA Toolkit.
   http://developer.nvidia.com/object/cuda_archive.html

3. Run crsample.exe to start the application in interactive mode. The first
   run executes certain initialization tasks that may take a while to
   complete.

4. If you get an error during initialization, the most probable explanation
   is that the application is unable to launch nvcc.exe contained in the
   CUDA Toolkit. In this case, you should:

   - Set CUDA_BIN_PATH to point to the CUDA Toolkit "bin" directory, e.g.
     "set CUDA_BIN_PATH=C:\Program Files (x86)\NVIDIA GPU Computing Toolkit\CUDA\v4.2\bin".

   - Set CUDA_INC_PATH to point to the CUDA Toolkit "include" directory, e.g.
     "set CUDA_INC_PATH=C:\Program Files (x86)\NVIDIA GPU Computing Toolkit\CUDA\v4.2\include".

   - Run vcvars32.bat to setup Visual Studio paths, e.g.
     "C:\Program Files (x86)\Microsoft Visual Studio 10.0\VC\bin\vcvars32.bat".

5. Run benchmark.cmd to measure the performance of CudaRaster and OpenGL on
   both test scenes with the same settings that were used in the paper.
   The script may take roughly 15 minutes to complete. The results are written
   into "benchmark.log", and are organized according to the Table and Figure
   numbering in the paper. The files "benchmark-gtxXXX-cudaYY.log", included
   in the package, contain reference results for different GPUs and CUDA
   versions.

6. Optional: Build the application manually.

   - Open cudaraster.sln in Visual Studio 2010.
   - Right-click the "crsample" project and select "Set as StartUp Project".
   - Build and run. Release/Win32 is recommended.


Package structure
-----------------

/crsample.exe                       Pre-built binary for the sample app.
/cudaraster.sln                     Visual Studio 2010 solution file.

/benchmark.cmd                      Script to run the benchmarks.
/benchmark.log                      Benchmark results.
/<scene>_cam<num>.png               Result images.
/benchmark-gtxXXX-cudaYY.log        Reference results (GTX XXX, CUDA Y.Y).
/state_crsample_<num>.dat           State files, exported with Alt-<num>.
/screenshot_crsample_<code>.png     Screenshots, exported with PrtScn.

/scenes/                            Test scenes in Wavefront OBJ format.
/cudacache/                         Temporary directory for CUDA binaries.
/build/                             Temporary directory for VS builds.

/src/cudaraster/                    Sources for the rasterizer.
/src/cudaraster/CudaRaster.hpp      Host-side public interface.
/src/cudaraster/cuda/PixelPipe.hpp  Device-side public interface.

/src/crsample/                      Sources for the sample application.
/src/crsample/App.cpp               Interactive mode.
/src/crsample/Benchmark.cpp         Benchmark mode.
/src/crsample/Shaders.cu            Device-side shader code.

/src/framework/                     General-purpose utility classes.


Version history
---------------
Version 1.1, June 02, 2019
- Upgrade to Visual Studio 2019 (previously 2010)
- Fix an OpenGL pixel format not found issue.

Version 1.1, May 22, 2012
- Fix incorrect pixel coverage computation with CUDA 4.1 and above.
- Fix incorrect ROP ordering with Kepler-based GPUs.
- Switch to New BSD License (previously Apache License 2.0).
- Upgrade to Visual Studio 2010 (previously 2008).
- Support PNG textures through lodepng.
- Fix a CUDA compilation issue with Visual Studio Express.
- General bugfixes and improvements to framework.

Version 1.0, Jul 08, 2011
- Initial release.


Known issues
------------

- The maximum viewport size is limited to 2048x2048, due to 32-bit fixed
  point math used in edge functions and plane equations.

- Subpixel resolution (4 bits) is lower than in the hardware pipeline
  (8 bits).

- Attribute precision is also lower. Upper bound for relative error is 2^-15
  without multisampling and 2^-12 with 8x MSAA. Depth, however, is very
  accurate.

- The overall memory footprint of the current implementation is relatively
  high, roughly 90 bytes per triangle in one batch. This can be alleviated
  by splitting larger models into multiple batches.

- The frame buffer is limited to 32-bit color and 32-bit depth. Support for
  other formats (e.g. float4) may be added in the future.

- The support for mesh and image formats is very limited. In particular,
  only Wavefront OBJ meshes and truecolor PNG/TGA/TIFF/BMP textures are
  supported. If you have trouble importing a mesh, you may want to try
  enabling WAVEFRONT_DEBUG in src/framework/io/MeshWavefrontIO.cpp.


Acknowledgements
----------------

University of Utah for the Fairy model.
Brian Curless and Marc Levoy the Happy Buddha model.
