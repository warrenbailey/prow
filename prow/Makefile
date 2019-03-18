CHART_REPO := http://jenkins-x-chartmuseum:8080
NAME := prow
OS := $(shell uname)
VERSION := $(shell cat ./VERSION)

init: 
	helm init --client-only

setup: init
	helm repo add jenkins-x http://chartmuseum.jenkins-x.io 	

build: clean setup
	helm dependency build prow
	helm lint prow

install: clean build
	helm upgrade ${NAME} . --install prow

upgrade: clean build
	helm upgrade ${NAME} . --install prow

delete:
	helm delete --purge ${NAME} prow

clean:
	rm -rf prow/charts
	rm -rf prow/${NAME}*.tgz
	rm -rf prow/requirements.lock

release: clean build
ifeq ($(OS),Darwin)
	sed -i "" -e "s/version:.*/version: $(VERSION)/" prow/Chart.yaml

else ifeq ($(OS),Linux)
	sed -i -e "s/version:.*/version: $(VERSION)/" prow/Chart.yaml
else
	exit -1
endif
	helm package .
	curl --fail -u $(CHARTMUSEUM_CREDS_USR):$(CHARTMUSEUM_CREDS_PSW) --data-binary "@$(NAME)-$(VERSION).tgz" $(CHART_REPO)/api/charts
	rm -rf ${NAME}*.tgz
