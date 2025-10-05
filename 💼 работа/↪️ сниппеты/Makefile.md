
```yaml
.PHONY: generate
generate: .generate-mocks .format-proto

.generate:
    $(MAKE) -f scratch.mk $@
    $(MAKE) .remove-invalid-generated-files
```



```
#  external_services_overrides:
#     usage: "Per services settings"
#     group: "scratch"
#     type: "string"
#     value: |
#       {
#         "express-couriers-delivery-route-api": {
#           "discovery": {
#             "target": "localhost:7102"
#           }
#         }
#       }
```