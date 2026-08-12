1、applogin，
网关接口地址：tif/yss/app/login
后端接口地址：/ebus/minshengwxmp/api/r/ysslogin/app/login
后端响应头：yss_login，有两种格式的头，第一种是法人版法人登录态（frb_frsf_，这里同时调用后端/ebus/minshengwxmp/api/r/yssuser/GetCorpCode接口获取code用于其他网关的登录态同步），第二种是个人版登录态（这里的同步是用identity中的/ebus/minshengwxmp/api/r/ysslogin/app/getSession接口实现）
```
{
    "name": "广州咻咻咻电子商务有限责任公司",
    "account": "eeaed88f",
    "account_type": "corp",
    "uid": "646226ec4c634543aaa2d64767081755",
    "cid": "91440111MAE8AWMG**",
    "ctype": "49",
    "mobile": "184821224**",
    "login_type": "PASSWORD",
    "level": "L1",
    "origin": "",
    "sex": "",
    "times": 0,
    "uversion": "",
    "legal_person": "",
    "legal_person_cid": "",
    "legal_person_ctype": "",
    "link_person_cid": "5105211996011853**",
    "link_person_ctype": "10",
    "link_person_name": "邓影",
    "sid": "jZJbsme7sg5xWMIcqjwQeCBHHecUO9IN5k1wsG3BVyxI22uvn2pVtlNBxyzgyObMCYMMRAfHwM1RWSPQh1gN9g",
    "auth_uid": "089d82139f034cb7b6b70fc7746022da"
}


{
    "session_id": {
        "sid": "55MYZT8UpHP08DglKPNrz2BEnJCcS9Bz-W0X8mJIRikSNas4zFLf4XTdn_8VL_Jecx1SBATR0kuLmdB0bxTbInw",
        "old_sid": "66MYZT8UpHP08DglKPNrz2BEnJCcS9Bz-W0X8mJIRikSNas4zFLf4XTdn_8VL_Jecx1SBATR0kuLmdB0bxTbInw"
    },
    "session_info": {
        "uid": "10145e00be06635046f8aeee214a35de1b67",
        "cid": "3506241989041540**",
        "openid": "",
        "cid_type": "1000",
        "name": "黄文洲",
        "star_cid": "350************054",
        "star_name": "黄*洲",
        "mobile": "",
        "session_key": "",
        "face_time": 0,
        "realname_time": 1769477102,
        "realname_checked": 1,
        "ctype": "10",
        "account_type": "human",
        "account": "A189339059**",
        "level": "L2",
        "project": "yss",
        "platform": "app",
        "login_type": 5,
        "yss_uid": "10145e00be06635046f8aeee214a35de1b67",
        "app": {
            "deviceid": "f89bf74a-cbdd-4c9a-a4d8-02dd50d8301f",
            "auth_uid": "5f09c5252b4a4ef9970b74d85859b27b"
        },
        "extension": {
            "cid_md5": "77aba78653ecc8d929f4d422534b9647"
        }
    },
    "session_ctl": {
        "expire_time": 6000,
        "face_expire_time": 0,
        "realname_expire_time": 300
    }
}
```
2、个人版登录态换取法人版个人登录态
网关接口：/dgdyssrz/sidtosession/
cookie标志：frb_grsf_{sid}
```
{
      ctype: user.session_info.ctype,
      cid: user.session_info.cid,
      account: user.session_info.account,
      name: user.session_info.name,
      uid: user.session_info.app.auth_uid,
      tif_uid: user.session_info.app.auth_uid,
      mobile: user.session_info.mobile,
      level: user.session_info.level,
      account_type: user.session_info.account_type,
      address: user.session_info.address,
      login_type: getLoginType(user.session_info.login_type),
      face_time: (user.session_info.login_type == 'SG-GASMHS' || user.session_info.login_type == '4') ? Date.now() : 0,
      face_authtime: (user.session_info.login_type == 'SG-GASMHS' || user.session_info.login_type == '4') ? Date.now() : 0,
      realname_time: getLevel(user.session_info.level) >= 2 ? Date.now() : 0,
      realname_checked: getLevel(user.session_info.level) >= 2 ? 1 : 0,
      link_person_name: user.session_info.link_person_name,
      link_person_cid: user.session_info.link_person_cid, //粤信签登陆取不到
      link_person_ctype: user.session_info.link_person_ctype, //粤信签登陆取不到
      tokenid: user.session_info.tokenid, //粤信签登陆无此信息
      origin: user.session_info.origin,
      uversion: user.session_info.uversion,
      expire_time: user.session_ctl.expire_time,
      corp: user.session_info.corp
    }
```
3、法人版法人登录态同步
网关接口：/dgdyssrz/codetosession/
后端接口：http://127.0.0.1/ebus/minshengwxmp/api/r/yssuser/CorpCodeToSession_OtherSeverRoom
作用：将applogin登录时的法人版法人登录态同步到其他网关