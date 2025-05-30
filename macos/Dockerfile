FROM ubuntu:22.04 AS builder

# Install Vulkan headers and dev tools
RUN apt-get update && \
    apt-get install -y git cmake build-essential libvulkan1 libgl1-mesa-dev libvulkan-dev

# Copy local source folder into the image
## I changed one line in the source code in main.cpp 
## const bool enableValidationLayers = false;
COPY ./vulkan_minimal_compute /vulkan-demo
WORKDIR /vulkan-demo
RUN cmake . && make

# --- Runtime container ---
FROM ubuntu:22.04

# Install Vulkan loader (runtime)
RUN apt-get update && apt-get install -y libvulkan1
RUN apt-get update && apt-get install -y \
    libvulkan1 \
    libvulkan-dev \
    vulkan-validationlayers \
    mesa-vulkan-drivers \
    && rm -rf /var/lib/apt/lists/*

# Copy binary from builder
COPY --from=builder /vulkan-demo/vulkan_minimal_compute /vulkan_minimal_compute

CMD ["/vulkan_minimal_compute"]
