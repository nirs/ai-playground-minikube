### Instructions
```
minikube start -d krunkit 
kubectl apply -f daemon.yaml

minikube docker-env
docker build -t local/d1 .

kubectl apply -f demo.yaml
kubectl logs vulkan-demo

##### the vulkan_minimal_compute is a clone of https://github.com/Erkaman/vulkan_minimal_compute except enableValidationLayers = false in main.cpp
 ```

