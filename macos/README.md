
# Instructions

## Pre-requsite 
```console
### Checkout the minikube's WIP: [Krunkit PR]([url](https://github.com/kubernetes/minikube/pull/20826))
gh repo clone kubernetes/minikube
gh pr checkout 20826


# install krunkit
$ brew tap slp/krunkit
$ brew install krunkit

# install vmnet-helper
# Follow https://github.com/nirs/vmnet-helper?tab=readme-ov-file#installation

# Build Docker Image
$ make

# Push the image to the registry:
$ make push
```


Start the cluster:

```console
minikube start -d krunkit
kubectl apply -f daemon.yaml
```

## Running the vulkan demo

Remove the uvkc pods if needed:

```console
kubectl delete pod uvkc-gpu --ignore-not-found
kubectl delete pod uvkc-cpu --ignore-not-found
```

Deploy the demo pods:

```console
kubectl apply -f demo-gpu.yaml
kubectl apply -f demo-cpu.yaml
```

Check that the GPU is detected in the demo container:

```console
% kubectl exec pod/vulkan-demo-gpu -it -- vulkaninfo --summary | grep GPU
GPU0:
    deviceType         = PHYSICAL_DEVICE_TYPE_INTEGRATED_GPU
    deviceName         = Virtio-GPU Venus (Apple M2 Max)
GPU1:
```

Run the demo program using the GPU:

```console
% kubectl exec pod/vulkan-demo-gpu -- sh -c 'time /vulkan_minimal_compute'

real    0m0.357s
user    0m0.317s
sys     0m0.008s
```

Run the demo program using the CPU:

```console
% kubectl exec pod/vulkan-demo-cpu -- sh -c 'time /vulkan_minimal_compute'

real    0m0.656s
user    0m0.940s
sys     0m0.012s
```

## Running the uVkCompute benchmarks

Remove the demo pods if needed:

```console
kubectl delete pod vulkan-demo-gpu --ignore-not-found
kubectl delete pod vulkan-demo-cpu --ignore-not-found
```

Deploy the uvkc pods:

```console
kubectl apply -f uvkc-gpu.yaml
kubectl apply -f uvkc-cpu.yaml
```

List the benchmarks:

```console
% kubectl exec pod/uvkc-gpu -it -- ls -1 /uvkc
atomic_reduce
conv2d_adreno
conv2d_mali_valhall
copy_sampled_image_to_storage_buffer
copy_storage_buffer
dispatch_void_shader
mad_throughput
matmul_tiled_adreno
matmul_tiled_mali_valhall
mmt_adreno
mmt_mali_valhall
one_workgrop_argmax
one_workgroup_reduce
subgroup_arithmetic
tree_reduce
vmt_rdna3
```

See https://github.com/google/uVkCompute/tree/main/benchmarks for
benchmark documentation.

Running a benchmark:

```console
% kubectl exec pod/uvkc-gpu -it -- /uvkc/mad_throughput
2025-06-01T18:53:04+00:00
Running /uvkc/mad_throughput
Run on (2 X 48 MHz CPU s)
Load Average: 0.22, 0.32, 0.29
-----------------------------------------------------------------------------------------------------------------------------------------
Benchmark                                                                               Time             CPU   Iterations UserCounters...
-----------------------------------------------------------------------------------------------------------------------------------------
Virtio-GPU Venus (Apple M2 Max)/mad_throughput_f32/1048576/100000/manual_time      458931 us          605 us            2 FLOps=4.56965T/s
Virtio-GPU Venus (Apple M2 Max)/mad_throughput_f32/1048576/200000/manual_time      916707 us         1054 us            1 FLOps=4.5754T/s
Virtio-GPU Venus (Apple M2 Max)/mad_throughput_f16/1048576/100000/manual_time      419236 us          561 us            2 FLOps=5.00232T/s
Virtio-GPU Venus (Apple M2 Max)/mad_throughput_f16/1048576/200000/manual_time      837492 us         1172 us            1 FLOps=5.00817T/s
llvmpipe (LLVM 17.0.6, 128 bits)/mad_throughput_f32/1048576/100000/manual_time   48959255 us         41.5 us            1 FLOps=42.8346G/s
llvmpipe (LLVM 17.0.6, 128 bits)/mad_throughput_f32/1048576/200000/manual_time   48864610 us         27.4 us            1 FLOps=85.8352G/s
llvmpipe (LLVM 17.0.6, 128 bits)/mad_throughput_f16/1048576/100000/manual_time  189785214 us         42.2 us            1 FLOps=11.0501G/s
llvmpipe (LLVM 17.0.6, 128 bits)/mad_throughput_f16/1048576/200000/manual_time  187865057 us         39.7 us            1 FLOps=22.3262G/s
```
