# go-basic-websocket

Simple WebSocket client

Example program using https://www.websocket.org/echo.html:

```go
package main

import (
	"fmt"
	"github.com/dnsge/go-basic-websocket"
	"net/http"
	"net/url"
	"sync"
	"time"
)

func main() {
	// simple echo websocket
	u := &url.URL{Scheme: "wss", Host: "echo.websocket.org", Path: "/"}
	headers := &http.Header{
		"User-Agent": []string{"echo-client/1.0"},
	}

	ws := basicws.NewBasicWebsocket(u, headers)
	wg := sync.WaitGroup{}

	ws.OnConnect = func() {
		fmt.Println("Connected to server!")

		time.AfterFunc(time.Second*3, func() {
			ws.SendString("Hello, world!")
		})
	}

	ws.OnMessage = func(b []byte) error {
		fmt.Printf("Received message: %s\n", string(b))
		ws.Disconnect()
		wg.Done()
		return nil
	}

	wg.Add(1)
	_ = ws.Connect()

	wg.Wait()
}
```

Output:
```text
Connected to server!
Received message: Hello, world!
```
