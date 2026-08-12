旧网关：
```
let ip = req.sp_targetHeaders ? req.sp_targetHeaders['x-real-ip'] || '' : '';  
if (new RegExp('^19\\.15\\.218\\.68').test(ip)) {  
    return Promise.reject({  
        statusCode: 401,  
        body: 'ip not access'  
    });  
}
```
新网关：
```
let ip = context.reqRealIP;  
if (!new RegExp('^19\\.').test(ip)) {  
  return {  
    statusCode: 403,  
    headers: {},  
    body: 'invalid ip'  
  };  
}
```