FROM ubuntu:22.04 AS builder

# Install Vulkan headers and dev tools
RUN apt-get update && \
    apt-get install -y git cmake build-essential libvulkan1 libgl1-mesa-dev libvulkan-dev

COPY ./vulkan_minimal_compute /vulkan-demo
WORKDIR /vulkan-demo
RUN cmake -DCMAKE_BUILD_TYPE=Release . \
    && make

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

# Shaders are accesssed in runtime
COPY ./vulkan_minimal_compute/shaders /shaders

CMD ["/vulkan_minimal_compute"]
