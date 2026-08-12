1、静默登录，/ebus/minshengwxmp/api/r/yssmplogin/MiniProgramLogin,返回头yss_mplogin,redis key值dgd:yss:auth:${id}，**登录态data是由Object.assign({}, b64Obj.session_id, b64Obj.session_info, b64Obj.session_ctl)组成;**
```
{
    "session_id": {
        "sid": "YSSRZf7240a380760b0491e09f7803bd0ca3df2f0"
    },
    "session_info": {
        "uid": "610b8b2d18e906d000562b59",
        "openid": "oDBw65As3uaMqkdVzygf5lqRmQdw",
        "unionid": "",
        "session_key": "OwRqXYTJ0U3J3BTW7z8ww",
        "cid": "44022919***",
        "cid_type": "1000",
        "name": "陈福院",
        "star_cid": "440************635",
        "star_name": "陈*院",
        "phone": "136000*****",
        "sid_time": 1742284233,
        "face_time": 1742284233,
        "realname_time": 1742284233,
        "realname_checked": 1,
        "cid_start_date": "",
        "cid_expire_date": "",
        "ctype": "10",
        "account_type": "human",
        "account": "chenfy1234**",
        "level": "L2",
        "project": "yss",
        "platform": "mp",
        "login_type": 14,
        "yss_uid": "",
        "extension": {
            "auth_status": 2,
            "auth_type": 14,
            "cid_md5": "012bb87ea2f9da9d4115bfcf583ccd70"
        }
    },
    "session_ctl": {
        "expire_time": 3600,
        "face_expire_time": 300,
        "realname_expire_time": 300
    }
}

```
2、identity逻辑
```
rf.assignDgdYssIdentity = (paasid, req, protocol, res, callback) => {
    let sid = req.headers['x-tif-sid'] || '';
    rf.writeLog('dgdyss',['identity sid: ',sid])
    getFromRedis('dgd:yss:auth:' + sid).then((ret) => {
      let s = req.sessData = JSON.parse(ret);
      let ext = s.ext_data ? s.ext_data : '';
      let extstr = '';
      if (ext) {
        let str = new Buffer(ext, 'base64').toString();
        ext = JSON.parse(str);
        ext.name = s.name;
        ext.face_expire = s.face_time && s.face_time + s.face_expire_time;
        ext.realname_expire = s.realname_time && s.realname_time + s.realname_expire_time;
        extstr = JSON.stringify(ext);
      } else {
        extstr = JSON.stringify({
          'name': s.name
        });
      }
      callback(null, {
        UserId: s.uid || '',
        UserInfo: s.cid || '',
        ExtData: Buffer.from(extstr).toString('base64'),
        tags: ['0']
      });
    }).catch((err) => {
      res.writeHead(200, {
        'Content-Type': 'application/json'
      });
      res.end(err.toString());
    });
  };
```
3、同步接口^/dgdyssrz/syncuser/，后端接口http://127.0.0.1/ebus/minshengwxmp/api/r/yssuser/CodeToSession_OtherSeverRoom，这里是前端调用登录侧网关得getcode ebus接口，然后在同步侧调用syncuser同步登录态
