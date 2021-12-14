### Swagger

#### Comments

```go
package v1

// FindLeave
// @Security Bearer
// @Accept json
// @Produce json
// @Success 201 {object} resp.Resp "success"
// @Tags Leave
// @Description FindLeave
// @Param params query request.Leave true "params"
// @Router /leave/list [GET]
func FindLeave(c *gin.Context) {
  // ...
}
```

You can add swagger comments in /api/v1/xxx function.

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/swagger/v1.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/swagger/v1.jpeg?raw=true" width="600" />
</a>

#### Update

```shell
# install swag
go get -u github.com/swaggo/swag/cmd/swag

cd gin-web
# update comments to swagger docs
swag init -o docs/swagger --pd --parseInternal 

```

After run `swag init` gin-web/docs/swagger/** will be changed.

#### View

You can view api list in [127.0.0.1/swagger/index.html](http://127.0.0.1/swagger/index.html)

#### More: [swag](https://github.com/swaggo/swag)
