include ../crd.Makefile
include ../gcloud.Makefile
include ../var.Makefile
include ../images.Makefile

CHART_NAME := trino
APP_ID ?= $(CHART_NAME)

TRACK ?= latest
SERVICE_NAME := services/e6580ff4-fda7-4750-b7a4-aeec14eecc6a.cloudpartnerservices.goog


VERIFY_WAIT_TIMEOUT = 1800

SOURCE_REGISTRY ?= us-central1-docker.pkg.dev/festive-vim-466116-c7/trino
IMAGE_TRINO ?= $(SOURCE_REGISTRY)/trino:$(TRACK)

# Main image
image-$(CHART_NAME) := $(call get_sha256,$(IMAGE_TRINO))

C2D_CONTAINER_RELEASE := $(call get_c2d_release,$(image-$(CHART_NAME)))

BUILD_ID := $(shell date --utc +%Y%m%d-%H%M%S)
RELEASE ?= $(C2D_CONTAINER_RELEASE)-$(BUILD_ID)
NAME ?= $(APP_ID)-1


APP_PARAMETERS ?= { \
  "name": "$(NAME)", \
  "namespace": "$(NAMESPACE)" \
}

# c2d_deployer.Makefile provides the main targets for installing the application.
# It requires several APP_* variables defined above, and thus must be included after.
include ../c2d_deployer.Makefile

# Build tester image
app/build:: .build/$(CHART_NAME)/tester