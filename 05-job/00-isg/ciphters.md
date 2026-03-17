网关支持的加密套件：
```
[
            // TLS 1.3 套件(自动具有前向保密)
            'TLS_AES_256_GCM_SHA384',
            'TLS_CHACHA20_POLY1305_SHA256',
            'TLS_AES_128_GCM_SHA256',
            'TLS_AES_128_CCM_8_SHA256',
            'TLS_AES_128_CCM_SHA256',

            // TLS 1.2 套件(仅 ECDHE)
            'ECDHE-ECDSA-AES256-GCM-SHA384',
            'ECDHE-RSA-AES256-GCM-SHA384',
            'ECDHE-ECDSA-CHACHA20-POLY1305',
            'ECDHE-RSA-CHACHA20-POLY1305',
            'ECDHE-ECDSA-AES128-GCM-SHA256',
            'ECDHE-RSA-AES128-GCM-SHA256',

            'DHE-RSA-AES128-GCM-SHA256',
            'DHE-RSA-AES256-GCM-SHA384',
            'DHE-RSA-CHACHA20-POLY1305',

            // 新增 RSA 套件（无前向保密，作为兼容性后备）
            //'AES128-GCM-SHA256',
            //'AES256-GCM-SHA384',
            //'AES128-SHA256',
            //'AES256-SHA256',
        ]
```