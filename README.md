# livego-frontend-english

Front-end part for [gwuhaolin/livego](https://github.com/gwuhaolin/livego)

## set enviroment

```dotenv
VUE_APP_FLV_PATH="http://192.168.123.101:7001"
VUE_APP_HLS_PATH="http://192.168.123.101:7002"
```

It needs to be enabled all the time `flv`, because the data ( /streams) of flv needs to be used

## configuration reference

- iptables handle ports

    ```shell
    iptables -A INPUT -s 127.0.0.1 -p tcp --dport 6379 -j ACCEPT
    iptables -A INPUT -s 127.0.0.1 -p tcp --dport 7001 -j ACCEPT
    iptables -A INPUT -s 127.0.0.1 -p tcp --dport 7002 -j ACCEPT
    iptables -A INPUT -s 127.0.0.1 -p tcp --dport 8090 -j ACCEPT
    iptables -A INPUT -p TCP --dport 6379 -j REJECT
    iptables -A INPUT -p TCP --dport 7001 -j REJECT
    iptables -A INPUT -p TCP --dport 7002 -j REJECT
    iptables -A INPUT -p TCP --dport 8090 -j REJECT
    ```
  
- nginx configuration (proxy interface and video), it should be noted that `8090`the port will expose sensitive information, you need to decide whether to proxy

    ```editorconfig
    location ~ (.*\.(flv)$|/streams) {
      # Here you may need to add http auth to prevent issues
      proxy_pass  http://127.0.0.1:7001;
      proxy_set_header Host $proxy_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    location ~ .*\.(m3u8|ts)$ {
      proxy_pass  http://127.0.0.1:7002;
      proxy_set_header Host $proxy_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    
    #location ~ /(control|stat) {
    #  # Here you may need to add http auth to prevent issues
    #  proxy_pass  http://127.0.0.1:8090;
    #  proxy_set_header Host $proxy_host;
    #  proxy_set_header X-Real-IP $remote_addr;
    #  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    #}
    
    location / {
      # play page
    }
    ``` 

- nginx (proxy streaming), needs to install `ngx_stream_proxy_module`the module, for reference only

    ```editorconfig
    # Put this layer in the root nginx.conf, not necessarily in the same file as the pile above
    stream {
        map $server_addr $internalport {
            example.com 1936;
        }
        server {
            listen 444;
            proxy_connect_timeout 1s;
            proxy_pass 127.0.0.1:$internalport;
        }
    }
    ```

## Backend modification reference

Since the original version will not delete the old token when processing redis content, it is recommended to refer to [here](https://github.com/BANKA2017/livego/commit/3aedd0e6a6a3a04dfd6d6e930d558afb8c7549de) for modification

(translators note, you may find it easier to just clone `https://github.com/BANKA2017/livego` unless you have special requirements)
