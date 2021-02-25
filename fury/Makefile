define check
	kubectl config current-context | grep $1 || (echo ERROR: wrong cluster && exit 1)
endef

.PHONY: minikube
minikube:
	minikube start --vm-driver=virtualbox --kubernetes-version v1.18.14 --memory=4000m --cpus=4
	minikube addons enable metrics-server
	minikube addons enable ingress

.PHONY: fury
fury:
	$(call check, minikube)
	kustomize build . | kubectl apply -f -
