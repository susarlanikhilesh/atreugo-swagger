# atreugo-swagger

[atreugo](https://github.com/savsgio/atreugo) middleware to automatically generate RESTFUL API documentation with Swagger 2.0.

## Usage

### Start using it

1. Add comments to your API source code, [See Declarative Comments Format](https://swaggo.github.io/swaggo.io/declarative_comments_format/).
2. Download [Swag](https://github.com/swaggo/swag) for Go by using:

```sh
go get -u github.com/swaggo/swag/cmd/swag
```

3. Run the [Swag](https://github.com/swaggo/swag) at your Go project root path(for instance `~/root/go-peoject-name`),
   [Swag](https://github.com/swaggo/swag) will parse comments and generate required files(`docs` folder and `docs/doc.go`)
   at `~/root/go-peoject-name/docs`.

```sh
swag init
```

4. Download [atreugo-swagger](https://github.com/swaggo/atreugo-swagger) by using:

```sh
go get -u github.com/swaggo/fasthttp-swagger
go get -u github.com/swaggo/files
```

### Canonical example:

Now assume you have implemented a simple api as following:

```go
// A get function which returns a hello world string by json
func Helloworld(ctx *atreugo.RequestCtx) error {

   return ctx.TextResponse("Hello World")
}

```

So how to use atreugo-swagger on api above? Just follow the following guide.

1. Add Comments for apis and main function with atreugo-swagger rules like following:

```go
// @BasePath /api/v1

// PingExample godoc
// @Summary ping example
// @Schemes
// @Description do ping
// @Tags example
// @Accept json
// @Produce json
// @Success 200 {string} Helloworld
// @Router /example/helloworld [get]
func Helloworld(ctx *atreugo.RequestCtx) error {
    
   return ctx.TextResponse("Hello World")
}
```

2. Use `swag init` command to generate a docs, docs generated will be stored at
3. import the docs like this:
   I assume your project named `github.com/go-project-name/docs`.

```go
import (
   docs "github.com/go-project-name/docs"
)
```

4. build your application and after that, go to http://localhost:8080/swagger/index.html ,you to see your Swagger UI.

5. The full code and folder relatives here:

```go
package main

import (
   _ "github.com/go-project-name/docs"
   fasthttpSwagger "github.com/swaggo/fasthttp-swagger"
)
// @BasePath /api/v1

// PingExample godoc
// @Summary ping example
// @Schemes
// @Description do ping
// @Tags example
// @Accept json
// @Produce json
// @Success 200 {string} Helloworld
// @Router /example/helloworld [get]
func Helloworld(ctx *fasthttp.RequestCtx)  {
   
   return ctx.TextResponse("Hello World")
}

func main()  {
  	config := atreugo.Config{
		Addr: "0.0.0.0:1337",
	}

	a := atreugo.New(config)

	// This will server all swagger files under the /docs/* path.
	a.GET("/docs/{doc:*}", atreugoswagger.WrapHandler(atreugoswagger.InstanceName("swagger")))

	a.GET("/test", func(ctx *atreugo.RequestCtx) error {
		return ctx.TextResponse("Hello World")
	})

	err := a.ListenAndServe()
	if err != nil {
		panic(err)
	}
}