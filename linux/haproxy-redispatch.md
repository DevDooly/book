# HAPROXY REDISPATCH

<https://www.haproxy.com/documentation/haproxy-configuration-tutorials/service-reliability/retries/>

haproxy 는 기본적으로 Failover 가 아닌 Load Balancing 을 수행한다.  
때문에 특정 서버가 다운되었을 때 새로운 연결 요청이 올 때까지 다음 서버로 넘어가지 않는다. 

retry, redispatch 기능을 사용해 서버가 연결 실패하는 경우 자동으로 재연결을 시도하거나, 다른 서버로 요청을 한다.

## RETRY

기본적으로 haproxy 는 연결 실패 시 3번까지 재시도한다.

`default` or `backend` 타입에 `retries` 를 수정하여 조정한다.

```ini
backend webservers
  mode http
  balance roundrobin
  server s1 192.168.0.1:80 check
  server s2 192.168.0.2:80 check
  retries 4
  timeout connect 3s
```

재시도는 최대 4회, 간격을 3초로 설정한다.

## REDISPATCH

특정 서버에 연결이 게속 실패하는 경우 해당 옵션을 사용해서 요청을 재분배한다.

`option redispatch` (래디스페치 아니고 리 디스페치...)

```ini
backend webservers
  mode http
  balance roundrobin
  option redispatch
  server s1 192.168.0.1:80 check
  server s2 192.168.0.2:80 check
```

### retry-on

어떤 상황에서 재시도를 수행할지 세부적으로 설정.

```ini
backend webservers
  mode http
  balance roundrobin
  retries 3
  retry-on all-retryable-errors
  server s1 192.168.0.1:80 check
  server s2 192.168.0.2:80 check
```

모든 재시도 가능한 오류에 3번까지 재시도