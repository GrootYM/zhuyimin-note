1. 服务器中的 http.listenAndServer() 启动服务
2. 生成Server结构体， 调用其中的ListenAndServer()方法
	```
	func ListenAndServer(addr string, handler Handler) error {
		server := &Server{Addr: addr, Handler: handler}
		return server.ListenAndServer()
	}
	```
3. net.listen 对地址监听，获取到listener 传递给 server.Server()方法
```
	func (srv *Server) ListenAndServer() {
		if srv.shoutDown() {
			return ErrServerClosed
		}
		addr := srv.Addr
		if addr == "" {
			addr = "http"
		}
		ln, err := net.Listen("tcp", addr)
		if err != nil {
			return err
		}
		return srv.Server(ln)
	}
```
4. 主要进行了两项工作，一项是在`for`死循环里面，通过`l.Accept()`来接受请求返回连接`Conn`，并处理相关的报错，根据该连接创建对应的`goroutine`来执行`conn.serve(ctx)`，各个请求之间是相互不影响的，提高并发性能。 另一项工作是新建`context`用于管理这些生成的`goroutine`，`context`作为参数被传入；
```
1.  func (srv *Server) Serve(l net.Listener) error {
2.  if fn := testHookServerServe; fn != nil {
3.  fn(srv, l) // call hook with unwrapped listener
4.  }

5.  origListener := l
6.  l = &onceCloseListener{Listener: l}
7.  defer l.Close()

8.  if err := srv.setupHTTP2_Serve(); err != nil {
9.  return err
10.  }

11.  if !srv.trackListener(&l, true) {
12.  return ErrServerClosed
13.  }
14.  defer srv.trackListener(&l, false)

15.  baseCtx := context.Background()
16.  if srv.BaseContext != nil {
17.  baseCtx = srv.BaseContext(origListener)
18.  if baseCtx == nil {
19.  panic("BaseContext returned a nil context")
20.  }
21.  }

22.  var tempDelay time.Duration // how long to sleep on accept failure

23.  ctx := context.WithValue(baseCtx, ServerContextKey, srv)
24.  for { //循环逻辑，接收请求处理
25.  //有新的连接
26.  rw, err := l.Accept()
27.  if err != nil {
28.  select {
29.  case <-srv.getDoneChan():
30.  return ErrServerClosed
31.  default:
32.  }
33.  if ne, ok := err.(net.Error); ok && ne.Temporary() {
34.  if tempDelay == 0 {
35.  tempDelay = 5 * time.Millisecond
36.  } else {
37.  tempDelay *= 2
38.  }
39.  if max := 1 * time.Second; tempDelay > max {
40.  tempDelay = max
41.  }
42.  srv.logf("http: Accept error: %v; retrying in %v", err, tempDelay)
43.  time.Sleep(tempDelay)
44.  continue
45.  }
46.  return err
47.  }
48.  connCtx := ctx
49.  if cc := srv.ConnContext; cc != nil {
50.  connCtx = cc(connCtx, rw)
51.  if connCtx == nil {
52.  panic("ConnContext returned nil")
53.  }
54.  }
55.  tempDelay = 0
56.  //创建新的连接
57.  c := srv.newConn(rw)
58.  c.setState(c.rwc, StateNew) // before Serve can return
59.  //启动新的goroutine进行处理
60.  go c.serve(connCtx)
61.  }
62.  }
```
