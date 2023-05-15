.PHONY: build
build:
	GOBIN=$$(pwd) go install github.com/googlecodelabs/tools/claat@latest
	for i in $$(ls codelabs/*.md); do ./claat export -o codelabs $$i; done
	gulp build
	rm -f ./build/codelabs
	cp -r ./codelabs ./build/

