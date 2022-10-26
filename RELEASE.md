<!--
# Copyright 2018-2022, NVIDIA CORPORATION & AFFILIATES. All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#  * Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
#  * Redistributions in binary form must reproduce the above copyright
#    notice, this list of conditions and the following disclaimer in the
#    documentation and/or other materials provided with the distribution.
#  * Neither the name of NVIDIA CORPORATION nor the names of its
#    contributors may be used to endorse or promote products derived
#    from this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS ``AS IS'' AND ANY
# EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR
# PURPOSE ARE DISCLAIMED.  IN NO EVENT SHALL THE COPYRIGHT OWNER OR
# CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL,
# EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
# PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR
# PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY
# OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
-->

# Release Notes for 2.27.0

## New Freatures and Improvements

* Added an [example](https://github.com/triton-inference-server/python_backend/tree/r22.10/examples/jax) 
  to demonstrate the use of JAX in python models.

* Improved and enhanced [Server Wrapper API](https://github.com/triton-inference-server/developer_tools/tree/r22.10/server) 
  to include missing features such as 
  [decoupled model](https://github.com/triton-inference-server/server/blob/r22.10/docs/user_guide/decoupled_models.md) 
  and tracing support.

* Multiple concurrent models can be profiled and analyzed by Model Analyzer. See 
  [Multi-Model Search Mode](https://github.com/triton-inference-server/model_analyzer/blob/main/docs/config_search.md#multi-model-search-mode) 
  for additional details.



## Known Issues

* Triton's TensorRT support depends on the CUDA event synchronization. In some 
  rare cases the events may be triggered earlier than expected, causing Triton 
  to overwrite input tensors while they are still in use and leading to corrupt 
  input data being used for inference. If you encounter accuracy issues with 
  your TensorRT model, you can work-around the issue by 
  [enabling the output_copy_stream option](https://github.com/triton-inference-server/common/blob/r22.09/protobuf/model_config.proto#L843-L852) 
  in your model's configuration.

* When using a custom operator for the PyTorch backend, the operator may not be 
  loaded due to undefined Python library symbols. This can be work-around by 
  [specifying Python library in LD_PRELOAD](https://github.com/triton-inference-server/server/blob/r22.09/qa/L0_custom_ops/test.sh#L114-L117).

* Auto-complete may cause an increase in server start time. To avoid a start 
  time increase, users can provide the full model configuration and launch the 
  server with `--disable-auto-complete-config`.

* Auto-complete does not support PyTorch models due to lack of metadata in the 
  model. It can only verify that the number of inputs and the input names 
  matches what is specified in the model configuration. There is no model 
  metadata about the number of outputs and datatypes. Related PyTorch bug: 
  https://github.com/pytorch/pytorch/issues/38273

* Perf Analyzer stability criteria has been changed which may result in 
  reporting instability for scenarios that were previously considered stable. 
  This change has been made to improve the accuracy of Perf Analyzer results. 
  If you observe this message, it can be resolved by increasing the 
  `--measurement-interval` in the time windows mode or 
  `--measurement-request-count` in the count windows mode.

* Triton Client PIP wheels for ARM SBSA are not available from PyPI and pip will 
  install an incorrect Jetson version of Triton Client library for Arm SBSA. 

  The correct client wheel file can be pulled directly from the Arm SBSA SDK 
  image and manually installed.

* Traced models in PyTorch seem to create overflows when int8 tensor values are
  transformed to int32 on the GPU. 

  Refer to https://github.com/pytorch/pytorch/issues/66930 for more information.

* Triton cannot retrieve GPU metrics with MIG-enabled GPU devices (A100 and A30).

* Triton metrics might not work if the host machine is running a separate DCGM 
  agent on bare-metal or in a container.


