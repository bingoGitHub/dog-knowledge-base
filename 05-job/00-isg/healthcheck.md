1、网关启动时worker.WorkerLogId=0的worker会启动两个定时器：
	定时器1：该定时器会定时(healthCheckInterval)对所有配置了rule.customHealthCheck、rule.healthCheckPath的规则做健康检查，把检查得出的不可达后端ip记录到healthCheckErrorHosts，直到健康检查成功才去除。
	定时器2：该定时器会定时(固定10秒)将healthCheckErrorHosts同步给master，再由master同步给其他worker。
2、配置相关：
	全局配置：
```
	healthCheckInterval : 1000 // 定时间隔，单位毫秒
```
	单规则配置:
```
	{
        "healthCheckPath": "/healthcheck/",// 健康检查路径
        "validHealthCheckStatusCode":[200,404],// 正常请求包含的状态码，类型为数组
        "useHealthCheckConnect":true， // 启动该配置则只指判断服务是否可达，不判断状态码
        "customHealthCheck":"return false"， // 自定义健康检查逻辑，与上面其他配置只能二选一
    }
```
customHealthCheck示例:
```
const requests = rule.targetHost.map(h => {
    h = h.toLowerCase();
    let arr = h.split(':');
    let port = parseInt(arr[1], 10);
    let host = arr[0];
    let options = {};
    options.host = host;
    options.port = port;
    options.headers = {
        host:
            rule.targetHostHeader ||
            (rule.https === true ? h.replace(':443', '') : h.replace(':80', '')),
    };

    options.agent = false;
    options.path = rule.healthCheckPath;


    return new Promise((resolve) => {
        const net = rf.require('net');
        let client = net.Socket();
        client.on('connect', () => {
            client.pause();
            healthCheckErrorHosts[h] = 0;
            resolve();
        });
        client.on('error', error => {
            if (healthCheckErrorHosts[h]) {
                healthCheckErrorHosts[h]++;
                if (healthCheckErrorHosts[h] >= 2) {
                    //rf.writeLog('healthCheckErrorHosts', [JSON.stringify(healthCheckErrorHosts)]);
                }
            } else {
                healthCheckErrorHosts[h] = 1;
            };
            resolve();
        });
        client.connect(port, host);
    })
});
return Promise.all(requests);
```