# How to make an HTTPS Server

Have you ever wished you could put all your files somewhere and access them from all of your devices? Or maybe you've been working in Jupyter Notebook or RStudio, and you've wished you could show someone what you've created on your phone.

To do these things, and much more, you need a personal server. But any old server isn't enough -- if you want to access sensitive files or data from around the globe, you'll need HTTPS.

```go
package make_proxy

import(
    "log"
    "net/http"
    "net/http/httputil"
    "net/url"
)

func MakeProxy(og, target string){
    
    origin, _ := url.Parse(og)
    
    director := func(req *http.Request){
        req.Header.Add("X-Forwarded-Host", req.Host)
        req.Header.Add("X-Origin-Host", origin.Host)
        req.URL.Scheme = "http"
        req.URL.Host = origin.Host
    }
    
    proxy := &httputil.ReverseProxy{Director: director}
    
    http.HandleFunc("/",
        func(w http.ResponseWriter, r *http.Request){
            proxy.ServeHTTP(w,r)
        })
        
    cert := "/home/jc/file-home/go/pems/cert.pem"
    key := "/home/jc/file-home/go/pems/privkey.pem"
    
    log.Fatal(http.ListenAndServeTLS(target, cert, key, nil))

}
```