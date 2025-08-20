
```yaml
.PHONY: generate
generate: .generate-mocks .format-proto

.generate:
    $(MAKE) -f scratch.mk $@
    $(MAKE) .remove-invalid-generated-files
```

