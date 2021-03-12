define check
	kubectl config current-context | grep $1 || (echo ERROR: wrong cluster && exit 1)
endef

.PHONY: fury
fury:
	$(call check, minikube)
	kustomize build . | kubectl apply -f -
