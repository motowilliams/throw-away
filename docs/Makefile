export DOCS_DIR_NAME ?= content
export DOCS_SRC ?= docs
export PUBLISH_DIR ?= public
export REPO_URL ?= https://example.com/repo
export SITE_DIR ?= site
export SITE_NAME ?= my github pages site
export PROTOCOL ?= http
export DOMAIN_NAME ?= 127.0.0.1:8000
export SITE_URL ?= $(PROTOCOL)://$(DOMAIN_NAME)/
export LOCAL_PORT ?= 8000

ifeq ($(OS),Windows_NT)
SHELL := '/usr/bin/sh.exe'
PWD := $(shell sh.exe -c pwd)
else
SHELL := /bin/bash
endif

export BASH_CMD := $(SHELL) -c
export COMMIT_HASH ?= $(shell [[ -d ".git" ]] && git rev-parse HEAD)
export REPO_ROOT := $(shell [[ -z "${CI}" ]] && echo '/app' || echo $(PWD))
export VERSION ?= $(shell [[ -d ".git" ]] && git tag --sort=committerdate | grep -E '[0-9]' | tail -1 || echo 0.0.0)

export IMAGE_NAME ?= throw-away-builder
export IMAGE_VERSION ?= latest
export IMAGE_TAG = $(IMAGE_NAME):$(IMAGE_VERSION)
export ADDITIONAL_BUILD_ARGS ?=

export DOCS_SRC_PATH=$(REPO_ROOT)/$(DOCS_SRC)
export PUBLISH_PATH := $(REPO_ROOT)/$(PUBLISH_DIR)

export CONTENT_SITE_PATH=$(DOCS_SRC_PATH)/$(DOCS_DIR_NAME)
export DOCS_SITE_PATH=$(DOCS_SRC_PATH)/$(SITE_DIR)

ifdef CI
	export IMAGE_TAG=
	export DOCKER_COMMAND :=
else
	export DOCKER_COMMAND := docker run -it \
		-v $(PWD):$(REPO_ROOT) \
		--env CI=TRUE \
		--env COMMIT_HASH=$(COMMIT_HASH) \
		--env COMMIT_HASH_LENGTH=$(COMMIT_HASH_LENGTH) \
		--env CONTENT_SITE_PATH=$(CONTENT_SITE_PATH) \
		--env DOCS_DIR=$(DOCS_DIR_NAME) \
		--env DOCS_SITE_PATH=$(DOCS_SITE_PATH) \
		--env DOCS_SRC_PATH=$(DOCS_SRC_PATH) \
		--env LOCAL_PORT=$(LOCAL_PORT) \
		--env PUBLISH_PATH=$(PUBLISH_PATH) \
		--env REPO_ROOT=$(REPO_ROOT) \
		--env REPO_URL=$(REPO_URL) \
		--env SITE_DIR=$(SITE_DIR) \
		--env SITE_NAME="$(SITE_NAME)" \
		--env SITE_URL=$(SITE_URL) \
		--name $(IMAGE_NAME) \
		--rm \
		-w $(DOCS_SRC_PATH)
endif

docs_clean: ## Removes the content artifacts directories and archive
	$(eval CMD := rm -rf $(SITE_DIR).zip && rm -rf $(SITE_DIR) && rm -rf $(DOCS_SITE_PATH) && rm -rf $(PUBLISH_PATH) )
	@echo "Cleaning $(SITE_DIR).zip & $(SITE_DIR) in $(SITE_DIR)"
	$(DOCKER_COMMAND) $(IMAGE_TAG) $(BASH_CMD) '$(CMD)'

docs_build: ## Builds the content to the 'site' directory
	$(eval CMD := build )
	@echo "Building"
	$(DOCKER_COMMAND) $(IMAGE_TAG) $(BASH_CMD) '$(CMD)'

docs_publish: ## Builds the content to the 'site' directory
	$(eval CMD := \
	mkdir -p $(PUBLISH_PATH) && \
	cp -R $(DOCS_SITE_PATH)/* $(PUBLISH_PATH)/ \
	)
	@echo "Publishing"
	$(DOCKER_COMMAND) $(IMAGE_TAG) $(BASH_CMD) '$(CMD)'

docs_debug: ## Runs the docker image interactively for debugging purposes
	@$(DOCKER_COMMAND) $(IMAGE_TAG) $(CMD)

docs_docker_build: ## Build the docker image used for these make targets not within DIND
	docker build --pull $(ADDITIONAL_BUILD_ARGS) -t $(IMAGE_TAG) $(DOCS_SRC)/. -f $(DOCS_SRC)/Dockerfile

docs_package: build_docs ## Builds the content to a compressed archive
	$(eval CMD := cd $(DOCS_SRC_PATH) && zip -r ../$(SITE_DIR).zip ./)
	@echo "Packaging"
	$(DOCKER_COMMAND) $(IMAGE_TAG) $(BASH_CMD) '$(CMD)'

docs_serve: ## Runs the mkdocs server
	$(eval CMD := serve)
	$(DOCKER_COMMAND) -p $(LOCAL_PORT):$(LOCAL_PORT) --expose $(LOCAL_PORT) $(IMAGE_TAG) $(BASH_CMD) '$(CMD)'

print-%: ; @echo $*=$($*)

printenv:
	printenv | sort
