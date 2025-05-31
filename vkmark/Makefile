REGISTRY = quay.io
USER = nirsof
IMAGE = vkmark
TAG = v1
TOOL = podman

image_url = $(REGISTRY)/$(USER)/$(IMAGE):$(TAG)

build:
	$(TOOL) build -t $(image_url) .

push:
	$(TOOL) push $(image_url)
