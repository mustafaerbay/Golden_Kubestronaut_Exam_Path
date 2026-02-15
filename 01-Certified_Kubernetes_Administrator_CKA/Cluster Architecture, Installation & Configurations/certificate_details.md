```

controlplane /etc/kubernetes/pki ➜  openssl x509 -in apiserver.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 8211538096389345164 (0x71f53e645edfcf8c)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: Feb  6 11:29:33 2026 GMT
            Not After : Feb  6 11:34:33 2027 GMT
        Subject: CN = kube-apiserver
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:b2:06:3a:ed:59:8f:f5:16:1b:e1:2a:fc:b6:67:
                    94:f1:7d:63:77:ee:37:36:ee:d5:23:a6:3e:da:41:
                    f0:c3:f0:e5:0e:19:da:0e:ab:7a:70:50:f3:68:da:
                    49:29:82:30:71:b2:a5:c7:71:79:6f:8b:aa:f4:57:
                    dc:ca:9c:77:3f:14:38:b0:1b:c0:96:8e:a1:cd:cf:
                    1d:aa:e3:e0:be:e5:d0:18:d5:dc:32:2d:5e:51:ae:
                    1c:76:8c:af:1b:a2:56:d1:cb:e6:0c:84:c1:f1:27:
                    a0:d1:73:c1:17:ce:27:e6:c4:a1:fd:7a:5f:ed:1a:
                    11:64:f0:dd:89:04:7e:1e:82:fa:78:33:a3:bb:ad:
                    8a:30:e2:b9:c7:16:b7:c7:08:86:b9:7e:2c:93:d6:
                    67:ca:14:97:98:57:e7:05:3f:3d:92:34:69:0a:39:
                    2e:5b:d5:ca:42:ef:8e:e8:f4:2e:bf:b4:a7:0e:3c:
                    44:e9:81:20:c3:b4:c4:c7:ec:af:c2:78:9f:5e:4c:
                    5f:44:37:81:62:3a:14:11:dc:1a:6d:91:26:98:b1:
                    1c:f7:24:3c:f2:68:73:aa:c7:ea:24:a4:8c:31:54:
                    fa:ed:8a:84:a1:b1:9a:66:2a:a3:33:93:64:a5:37:
                    f8:6f:6d:26:2b:a2:d8:80:96:7f:cf:75:4a:67:c2:
                    05:15
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Authority Key Identifier: 
                96:74:45:56:1C:17:61:19:BD:1A:53:A5:11:68:4C:86:08:96:0A:6A
            X509v3 Subject Alternative Name: 
                DNS:controlplane, DNS:kubernetes, DNS:kubernetes.default, DNS:kubernetes.default.svc, DNS:kubernetes.default.svc.cluster.local, IP Address:172.20.0.1, IP Address:10.244.55.232
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        9c:a6:23:17:ee:5e:8a:77:94:89:fd:7d:a6:77:a4:be:2a:7b:
        70:f5:c2:c1:6c:65:d2:6d:32:70:90:37:b3:21:2a:ce:cf:23:
        57:8a:d8:5b:65:7b:40:75:84:9b:4c:dc:70:84:b9:f8:74:54:
        dc:4f:6b:c1:b3:79:08:23:d3:9b:de:4a:f7:3d:fc:4b:39:11:
        15:3b:b9:01:84:9d:4d:23:bb:e3:16:3b:d4:10:9b:70:3e:d3:
        19:b2:d6:50:d2:9c:df:8c:b7:2f:df:24:06:a9:7c:b5:82:36:
        b4:b8:ac:f8:e7:4f:0b:ff:c3:d4:c8:9e:3e:9e:9f:92:d5:95:
        86:e7:39:b3:f8:c8:ec:a0:29:a4:34:6b:81:6e:dc:da:db:54:
        62:4a:49:63:85:a4:3d:1d:87:e5:90:70:5c:be:cc:84:cb:5f:
        1a:0f:be:3f:d3:08:90:8e:5e:7e:29:24:c5:6e:5e:1e:a6:c9:
        0a:53:a1:8e:6a:04:55:2d:57:46:35:c1:75:69:58:4e:ca:c4:
        53:62:35:84:19:7f:bb:86:58:5d:a2:8e:33:b1:1d:e5:2c:f4:
        3c:bb:f2:2d:08:a0:86:07:bd:bd:ea:57:5d:0a:38:d6:d8:55:
        d9:4b:f9:64:e3:d2:35:57:00:aa:27:5e:9c:d4:b1:1f:89:ae:
        2e:90:36:16
```
```

controlplane /etc/kubernetes/pki ➜  cd etcd/

controlplane kubernetes/pki/etcd ➜  openssl x509 -in server.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 8449252293442531886 (0x7541c631e04bf22e)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = etcd-ca
        Validity
            Not Before: Feb  6 11:29:33 2026 GMT
            Not After : Feb  6 11:34:33 2027 GMT
        Subject: CN = controlplane
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:b1:a5:ae:d3:f5:f2:ef:8f:4c:cd:9c:af:91:78:
                    65:37:ce:91:64:7f:31:9a:1e:a7:62:40:7b:9b:9d:
                    fd:d7:e8:4f:1a:cf:1c:bc:52:50:cb:eb:a2:9d:55:
                    5c:94:a3:1b:92:5c:e5:04:84:62:78:aa:3c:5f:74:
                    7c:88:35:09:96:5c:42:c6:a6:7b:0f:57:2b:85:76:
                    4e:91:cb:2d:84:c7:2d:51:2e:65:7d:7d:4d:ab:c3:
                    5f:ad:1b:19:e4:a2:ab:40:3a:ef:aa:77:47:4c:e8:
                    0c:42:31:13:a6:67:89:49:34:72:ac:18:d7:40:82:
                    3c:a2:ec:33:3a:95:49:99:47:12:c9:12:e9:d6:3d:
                    7b:5e:cb:5d:83:2e:6d:34:9b:a5:41:e6:ba:f9:17:
                    ed:48:51:e1:64:d7:62:52:f9:51:df:00:5f:53:83:
                    ff:d0:98:b5:1e:29:aa:d8:d2:c3:8a:d3:01:7d:3f:
                    20:d6:f6:35:d4:ec:d0:8d:5c:8f:e3:f3:9a:90:67:
                    ce:00:21:d9:17:49:0b:57:fe:46:84:80:94:fd:1f:
                    5c:80:60:c0:a0:cc:6f:44:52:a7:54:2e:16:37:bb:
                    82:3b:d3:1d:f7:1e:15:54:be:a0:1d:66:71:09:2f:
                    dc:dc:39:d8:e5:58:e0:c9:91:4c:87:f3:db:ed:b6:
                    d8:1f
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Authority Key Identifier: 
                C4:18:77:71:C2:E3:22:80:59:E8:7D:DE:DE:83:A2:AD:8A:EA:2E:4E
            X509v3 Subject Alternative Name: 
                DNS:controlplane, DNS:localhost, IP Address:10.244.55.232, IP Address:127.0.0.1, IP Address:0:0:0:0:0:0:0:1
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        8a:4c:6a:fe:3c:ef:1d:96:87:5c:5d:d0:ac:5f:95:34:4a:c1:
        21:2c:da:51:29:04:f4:13:d3:55:b0:e1:e3:14:d5:8a:f6:35:
        c0:17:e0:f0:19:9c:1e:da:33:c8:f5:ae:7f:30:2c:5a:43:ba:
        d7:43:8b:f7:85:3c:c4:5e:98:bd:9c:79:60:d7:ca:2a:7f:e5:
        6c:e9:03:ab:9c:c4:e4:44:6e:35:d0:f8:da:79:cc:4c:bf:02:
        17:08:6e:08:39:72:68:5f:69:fe:bb:17:3a:17:81:bd:e4:73:
        a6:14:d9:26:fc:b3:d1:39:0d:c7:90:91:ff:d4:cc:32:f5:55:
        e4:65:75:a5:31:08:1d:a5:6e:e0:1c:4d:0b:22:5b:ea:b7:65:
        76:8c:13:b1:06:a1:26:7c:16:6a:83:37:5b:a3:7e:60:0c:22:
        1d:ed:43:96:ec:d3:af:91:0a:99:1f:69:03:9e:c5:84:af:82:
        19:6a:e6:32:27:df:2c:b5:15:f4:30:6c:19:4a:52:81:8d:71:
        59:88:2c:39:d8:01:ec:0e:ed:ae:06:07:36:ad:99:35:0b:5c:
        f1:06:18:36:00:ff:c5:dd:5a:da:4b:1d:53:5b:1c:a8:94:73:
        b5:80:29:83:51:c6:39:00:a9:df:25:a8:51:2a:83:25:c5:04:
        be:ef:69:8e
```

```

controlplane kubernetes/pki/etcd ➜  cd ..

controlplane /etc/kubernetes/pki ➜  openssl x509 -in ca.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 9096881460451726229 (0x7e3e9d7dae0fd395)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: Feb  6 11:29:33 2026 GMT
            Not After : Feb  4 11:34:33 2036 GMT
        Subject: CN = kubernetes
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:d7:29:01:07:fe:46:43:c7:7b:9b:8d:6c:12:2f:
                    30:1b:95:63:6c:1e:da:30:50:24:a8:9d:1a:33:15:
                    48:15:b9:30:06:22:44:8f:c7:f1:88:f8:aa:e2:d7:
                    4c:99:db:87:d0:9c:d7:01:51:ce:22:09:09:88:42:
                    0b:53:8d:2c:19:73:7f:b8:26:c9:94:a2:91:dc:b2:
                    61:95:26:95:7e:d9:6e:26:9b:97:c1:87:35:57:b0:
                    60:ac:10:8b:75:e4:b6:36:78:3a:48:5b:67:2b:b5:
                    8d:c8:ed:71:1b:08:52:db:92:29:aa:d6:28:ec:c2:
                    52:d2:c4:7c:cd:e9:60:21:4b:ee:7e:c8:45:85:b5:
                    50:06:a3:66:9f:cd:0c:16:96:a2:4f:95:ca:7b:8b:
                    f1:84:d6:b6:d9:3d:e1:18:0b:2f:2d:57:23:0a:f3:
                    75:54:eb:1c:d4:c3:5f:52:dc:85:5b:cb:56:24:7b:
                    72:e0:63:61:62:b4:74:88:14:45:5a:d7:07:97:17:
                    19:5e:4e:db:34:40:d6:8e:7d:85:e3:70:78:aa:94:
                    ea:4b:cc:a9:bb:99:71:ba:9e:7d:9a:b4:a2:3e:73:
                    57:63:8f:e3:a5:4e:c9:34:dc:7c:be:88:b6:14:06:
                    5c:a6:60:8f:f9:33:74:1e:da:4a:ca:12:2d:12:18:
                    19:af
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment, Certificate Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Subject Key Identifier: 
                96:74:45:56:1C:17:61:19:BD:1A:53:A5:11:68:4C:86:08:96:0A:6A
            X509v3 Subject Alternative Name: 
                DNS:kubernetes
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        b2:8a:b2:77:f7:ad:85:1c:b4:a7:67:ae:55:a5:73:95:3a:f4:
        80:73:2c:b2:88:98:00:35:c4:1b:81:64:2b:41:6f:0c:4d:12:
        bf:99:d2:19:5f:3e:18:ff:4f:44:0a:86:c7:25:c6:ca:ae:2e:
        1b:c4:53:43:38:a3:e3:9d:08:2b:d8:72:bf:65:a1:71:7e:50:
        66:3a:66:26:2b:83:64:2f:eb:1d:41:93:6b:ec:c7:d9:4e:ef:
        b6:c4:b8:e0:6f:53:c3:85:c1:7d:21:68:70:34:08:4e:7b:b4:
        49:08:a3:22:95:61:d5:ce:85:17:75:16:52:28:59:fc:80:7f:
        36:3d:d2:e5:4a:0d:1d:0f:a1:ec:b6:a5:76:2d:c0:b4:10:a4:
        f5:0f:ec:26:52:a5:4a:6f:cd:1b:ac:59:64:ba:35:63:c6:10:
        3f:24:a0:31:c1:50:94:d7:8c:9b:7f:8b:45:61:bc:5d:d0:1c:
        98:6d:94:f0:6c:07:dd:28:e0:7b:e2:8a:44:04:16:68:85:c0:
        83:24:7d:44:fb:30:6f:63:2d:cd:c6:10:85:1e:39:9e:08:c2:
        a6:25:d1:76:e0:20:01:ad:fb:5b:64:e2:57:b0:55:34:9d:a5:
        df:d9:fd:84:64:ca:cb:1b:25:43:0e:16:81:c3:e5:a9:0c:04:
        ba:02:b5:ba

controlplane /etc/kubernetes/pki ➜  

```