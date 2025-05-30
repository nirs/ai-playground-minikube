# Instructions

Build the image:

```console
make
```

Push the image to the registry:

```console
make push
```

Start the cluster:

```console
minikube start -d krunkit
kubectl apply -f daemon.yaml
kubectl apply -f demo-gpu.yaml
kubectl apply -f demo-cpu.yaml
```

Check that the GPU is detected in the demo container:

```console
% kubectl exec pod/vulkan-demo -it -- vulkaninfo --summary | grep GPU
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
