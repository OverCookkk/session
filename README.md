调用方式：

import了memory，执行memory.go里面的init()函数，会进行注册储存器到操作。

```go
import (
	"github.com/astaxie/session"
	_ "github.com/astaxie/session/providers/memory"
)

var globalSessions *session.Manager

//然后在init函数中初始化
func init() {
    // 初始化session管理器
	globalSessions, _ = session.NewManager("memory", "gosessionid", 3600)
	go globalSessions.GC()	// 启动定时任务定时清理超过最大存活时间的session
}
```



login操作来演示session的运用：

```go
func login(w http.ResponseWriter, r *http.Request) {
	sess := globalSessions.SessionStart(w, r)
	r.ParseForm()
	if r.Method == "GET" {
		t, _ := template.ParseFiles("login.gtpl")
		w.Header().Set("Content-Type", "text/html")
		t.Execute(w, sess.Get("username"))
	} else {
		sess.Set("username", r.Form["username"])
		http.Redirect(w, r, "/", 302)
	}
}
```